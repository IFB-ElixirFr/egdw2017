class: middle

# Connecting Galaxy to a compute cluster

## EGDW 2017

Slides by Stéphanie Le Gras<br/>
Inspired by Galaxy Administrator Courses from [Dagobah](https://github.com/martenson/dagobah-training)

.footer[
https://github.com/igbmc/egdw2017
]

---

layout: true
name: title
class: middle

.footer[
EGDW 2017
]

---

layout: true
name: content

.footer[
EGDW 2017
]

---

layout: true
name: hands-on

.footer[
EGDW 2017 - **Hands-on**
]

---
template: content
# Why cluster?

Running jobs on the Galaxy server negatively impacts Galaxy UI performance

Even adding one other host helps

Can restart Galaxy without interrupting jobs

---
template: content
# Cluster options

- Slurm
- Condor
- Torque
- PBS Pro
- LSF
- SGE derivatives maybe?
- Any other [DRMAA](https://www.drmaa.org/)-supported DRM

---
template: content
# Galaxy job configuration

`config/job_conf.xml`:
- From basic to advanced
- XML format
- Major components:
  - Plugins: Interface to DRMs
  - Destinations: Where to send jobs, and what parameters to run those jobs with
  - Handlers: Which job handler processes should handle the lifecycle of a job
  - Tool to destination/handler mappings: Specify that a tool should be sent to a specific destination
  - Resource selection mappings: Give users job execution options on the tool form
  - Limits: Set job runtime limits such as the max number of concurrent jobs

---
template: content

# Plugins

Correspond to job runner plugins in [lib/galaxy/jobs/runners](https://github.com/galaxyproject/galaxy/tree/dev/lib/galaxy/jobs/runners)

Plugins for:
- Slurm (DRMAA subclass)
- DRMAA: SGE, PBS Pro, LSF, Torque
- Condor
- Torque: Using the `pbs_python` library
- Pulsar: Galaxy's own remote job management system
- CLI via SSH
- Kubernetes
- Go-Docker

---
template: content
# Plugins

Most job plugins require a **shared filesystem** between the Galaxy server and compute.

The exception is **Pulsar**.

---
template: content
# Destinations

Define *how* jobs should be run:
- Which plugin?
- In a Docker container? Which one?
- DRM params (queue, cores, memory, walltime)?
- Environment (variables e.g. `$PATH`, source an env file, run a command)?

---
template: content
# Handlers

Define which job handler (Galaxy server) processes should handle a job:
- Higher availability pool for small, high throughput jobs
- Work around concurrency limit race conditions

---
template: content
# The default job configuration

`config/job_conf.xml.sample_basic`:
```xml
<?xml version="1.0"?>
<job_conf>
    <plugins>
        <plugin id="local" type="runner" load="galaxy.jobs.runners.local:LocalJobRunner" workers="4"/>
    </plugins>
    <handlers>
        <handler id="main"/>
    </handlers>
    <destinations>
        <destination id="local" runner="local"/>
    </destinations>
</job_conf>
```

---
template: content
# Shared Filesystem

Our simple example works because of two important principles:

1. Some things are located *at the same path* on Galaxy server and node(s)
  - Galaxy application (`/srv/galaxy/server`)<sup>[1]</sup>
  - Tool dependencies<sup>[1]</sup>
2. Some things *are the same* on Galaxy server and node(s)
  - Job working directory
  - Input and output datasets

The first can be worked around with symlinks or Pulsar embedded (later)

The second can be worked around with Pulsar REST/MQ (with a performance/throughput penalty)

.footnote[<sup>[1]</sup> A fix for this has been proposed

<sup>[2]</sup> Except conda dependencies!]

---
template: content
# Job Config - Tags

Both destinations and handlers can be grouped by **tags**:
- Allows random selection from multiple resources
- Allows concurrency limits on both destination and group level
- Decision-based selection available via dynamic job runner (later)

---
template: content
# Job Environment

`<env>` tag in destinations: configure the job exec environment:
- `<env id="NAME">VALUE</env>`: Set `$NAME` to `VALUE`
- `<env file="/path/to/file" />`: Source shell file at `/path/to/file`
- `<env exec="CMD" />`: Execute `CMD`

---
template: content
# Limits

Available limits:
- Walltime (if not available with your DRM)
- Output size (if *any* tool output grows larger than this limit)
- Concurrency: Number of "active" (queued or running) jobs

---
template: content
# Concurrency Limits

Available limits:
- Number of active jobs per registered user
- Number of active jobs per unregistered user
- Number of active jobs per registered user in a destination or destination tag
- Number of active jobs total in a destination or destination tag

---
template: content
# The advanced (full) job configuration

- [Sample advanced job config](https://github.com/martenson/dagobah-training/blob/master/advanced/005-compute-cluster/job_conf.sample_advanced.xml) (copied for syntax highlighting)
- [Sample advanced job config](https://github.com/galaxyproject/galaxy/blob/dev/config/job_conf.xml.sample_advanced) (canonical source from `config/job_conf.xml.sample_advanced`)
