class: center, middle

# Galaxy Configuration Hierarchy

## EGDW 2017

Slides by Julien Seiler<br/>
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

template: content

# Overall files hierarchy

## Main folders

- **config**: Contains the various configuration files

- **tools**: Contains basic tools and wrappers included with Galaxy

- **tool-data**: Contains data descriptors (.loc files).
Available genomes are declared in the file `tool-data/shared/ucsc/builds.txt`

- **static**: Containts static web pages and styles for your instance.
Modify the file `static/welcome.html` to customize your welcome page.

- **database**: Default location for users data

- **logs**: Contains instance's log files

---

# Overall files hierarchy

## Configuration files

The main configuration files are located in the `config` directory.
They are created automatically by Galaxy during the first launch.

- **galaxy.ini**: main configuration of the instance

- **tool_sheds_conf.xml**: ToolShed accessible from the instance

- **tool_conf.xml**: tools "installed manually"

- **shed_tool_conf.xml**: tools deployed on the instance from a ToolShed

- **datatypes_conf.xml**: various data type declaration

- **job_conf.xml**: computing resources declaration (queue, memory…)

.callout.callout-info[
Galaxy often uses the file with suffix `*.sample` as declared defaults
]

---

## `galaxy.ini`

The `galaxy.ini` file is Galaxy's main configuration file. It indicates the relative or absolute location of all other Galaxy files or folders.

It is possible to specify a specific `galaxy.ini` file when launching Galaxy by setting the `GALAXY_CONFIG_FILE` environment variable.

```shell
[galaxy]$ GALAXY_CONFIG_FILE=config/galaxy.ini run.sh
```

.callout.callout-success[
In order to keep the sources of Galaxy unchanged and to facilitate your future updates, we recommend that you store all of your specific configuration files and folders outside of the source folder.

For example, you can create a `config` folder containing all of your configuration files next to your `galaxy` folder.
]

---

## `galaxy.ini`

The configuration file `galaxy.ini` is organized in sections allowing to parameterize the different components of Galaxy:

--

### The HTTP handlers

An HTTP handler is the process capable of translating an HTTP request (for example, sent by a user's browser) into a Galaxy application call

By default, only one HTTP handler is defined : `[server:main]`

---

## `galaxy.ini`

### Filters

Filters are located between the HTTP servers and the Galaxy application

They allow for example to define an algorithm of compression of the transferred data or to define an HTTP prefix for your Galaxy instance (`/galaxy` for example)

By default, no filter is used.

--

### The Galaxy app configuration

This section begins with the `[app: main]` marker.

It contains all Galaxy operating parameters (path, directories, databases, user authentication, security, etc.)<br/>
It also defines the location of the other configuration files
