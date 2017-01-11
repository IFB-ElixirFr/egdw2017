class: center, middle

# Galaxy Installation

## EGDW 2017

Björn Grüning | Stéphanie Le Gras | Julien Seiler

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

# Getting the source code

The most common way to install Galaxy is to download the source code of the project using Git.

## Get the source code of Galaxy (using Git)

```shell
[~]$ git clone https://github.com/galaxyproject/galaxy/
[~]$ cd galaxy
[galaxy]$ git checkout -b master origin/master
```

.callout.callout-danger[
#### Warning
The source code of Galaxy is already installed on your VM in `/usr/local/galaxy/galaxy`
]

---

# First start

You are ready to launch your Galaxy instance !

## Run Galaxy

```shell
[galaxy]$ sh run.sh
```

The `run.sh` script will initialize your Galaxy environment with default settings :

1. It creates and activates and Python virtual environment (located in .venv)
1. It retrieves Python packages required by Glaaxy
1. It initialize default config files
1. It initialize the Galaxy databse (based on SQLlite by default)

After one or two minutes you can access your instance by pointing your browser to http://localhost:8080/

---

# Run a job

Upload in your history the file `/usr/local/galaxy/galaxy/test-data/1.tabular`

--

1. Select the **Get Data / Upload File** tool
1. Click the **Choose local file** button in the dialog
1. Select the `1.tabular` file `/usr/local/galaxy/galaxy/test-data`
1. Click the **Start** button
1. When the file upload is finished, click the **Close** button

--

Find a tool to change the case of the third column of this dataset

--

1. Select the **Text Manipulation / Change case** tool
1. Make sure the **From** field points to your `1.tabular` dataset
1. Set the **Change case of columns** field to `c3`
1. Click the **Execute** button

A new dataset has been created in your history.

---

template: title

# Galaxy architecture

---

layout: true
template: content

# Galaxy architecture

---

## Overall files organization

### Main folders

- **config**: Contains the various configuration files

- **tools**: Contains basic tools and wrappers included with Galaxy

- **tool-data**: Contains data descriptors (.loc files).
Available genomes are declared in the file `tool-data/shared/ucsc/builds.txt`

- **static**: Containts static web pages and styles for your instance.
Modify the file `static/welcome.html` to customize your welcome page.

- **database**: Default location for users data

- **logs**: Contains instance's log files

---

## Overall files organization
### Configuration files


The main configuration files are located in the `config` directory.
They are created automatically by Galaxy during the first launch.

- **galaxy.ini**: main configuration of the instance

- **tool_sheds_conf.xml**: ToolShed accessible from the instance

- **tool_conf.xml**: tools "installed manually"

- **shed_tool_conf.xml**: tools deployed on the instance from a ToolShed

- **datatypes_conf.xml**: various data type declaration

- **job_conf.xml**: computing resources declaration (queue, memory…)

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

---

template: title

# Galaxy administration

---

layout: true
template: content

# Galaxy administration

---

## Getting administrator rights
### Creating a user account

From the Galaxy web interface, create an account by clicking on the **User** menu and then **Register**.

.callout.callout-info[
#### Hint
Galaxy is waiting for an email address as login.
]

.center[![User registration menu](images/user-menu.png)]

---

## Getting administrator rights

Administrators of a Galaxy instance are declared in the `galaxy.ini` file

```ini
admin_users = firstname.lastname@domain.com
```

.callout.callout-info[
- Declare the email address you used to register to your Galaxy instance
- It is possible to add several administrators by separating the email addresses with commas.
- It is necessary to restart Galaxy for these changes to be taken into account.
]

---

## The Galaxy admin interface

.row[
.col-md-4[
- Visualization of data types and data tables
- Management of jobs in progress

- Tools installation via toolshed
- Restarting an individual tool after modifying its XML wrapper

- Managing Users, Groups and Roles

- Managing shared data and libraries

- Managing storage space quotas
]
.col-md-8[
.image[![Administration interface](images/administration.png)]
]
]

---

## Group management

Galaxy allows to gather a set of users in one or more groups.<br/>
The use of groups makes it easier to assign quotas and roles to a set of users.

You can manage groups of your instance from the **Security/Groups** menu of the administration interface

--

### Exercice

Create the Admins group that will bring together the administrators of your instance.

--

.center[![Adding a group](images/add-group.png)]

---

## Quota management

Galaxy incorporates a mechanism to limit the storage space available to each user. These are quotas.

A quota is characterized by a quantity of storage space and an allocation mode.<br/>
It is possible to assign a quota to a user or a group of users.<br/>
A user or group can receive multiple quotas.

The amount of storage space is a number followed by a unit. For example: `1000MB` or` 2GB`
It is possible to create an "unlimited" quota using the value `unlimited`

A quota may be assigned:
- accurately : `=`
- by increasing the current quota : `+`
- by decreasing the current quota : `-`

The sum of the quota assignments of a user makes it possible to determine its exact quota.

---

## Quota management
### Exemple

The following quotas have been defined on a public instance:

.pure-table.pure-table-bordered[
| Name | Mode | Amount | Status |
| --- | --- | --- | --- |
| Registered users | `=` | 50GB | Default for users with an account |
| Anonymous | `=` | 100MB | Default for unauthenticated users |
| RNASeq users | `+` | 25GB | &nbsp; |
| Training | `-` | 49GB | &nbsp; |
]

During a training, we will create **a user account for each student**.
Each account will be assigned the **Training** quota.

The quota of a student will therefore be: <br/>
50GB (default) - 49GB (Training) = 1GB

---

## Quota management
### Enabling the quota system

By default, the quota system is disabled.

Modify the configuration file of your instance to activate the quota system:

```ini
enable_quotas = True
```

Restart your instance for the change to take effect.

---

Quota management is done from the Quotas menu in the Data section of the administration interface

### Exercice 1 :
Create a quota of 10MB for all anonymous users.

### Exercice 2 :
Create a 100MB quota for all users with an account.

### Exercice 3 :
Create an additional 100MB quota for Administrator users.<br/>
You can use the `Admins` group to assign this quota.

---

template: title

# Galaxy Handlers

---

template: content

# Galaxy Handlers
## Web handlers vs Job handlers

Galaxy is a web application that uses handlers to perform actions.

There are two main types of actions that are carried out by handlers:
- Respond to user requests; These actions are carried out by web handlers
- Manage the execution of tools; These actions are performed by job handlers.

By default, Galaxy is configured to run a single handler that handles user queries and jobs.

Depending on the number of users accessing your Galaxy instance or the number of jobs you need to manage you may need to start web handlers or additional job handlers.

---

layout: true
template: content

# Galaxy Handlers
## Sample architecture with two handlers

---

For a classical Galaxy instance we declare at least one handler dedicated to the execution of the jobs. This will allow us to separate the log specific to the execution of the tools and log about the actions of the user on the web interface.

Here is the configuration we would like to set up :

.center[![Target config](images/handlers.png)]

---

### Declare a new handler that will listen on port `8090`

In your `config/galaxy.ini` file, add the following lines at the end of the HTTP server section (line 50)

```ini
[server:job]
use = egg:Paste#http
port = 8090
use_threadpool = True
threadpool_kill_thread_limit = 10800
```

This section defines a new handler that will be launched by the `run.sh` script.

.pure-table.pure-table-bordered.smaller-font[
Parameter | Description
--- | ---
use = egg:Paste#http | The type of engine used to run this handler
port = 8090 | The handler will listen on port 8090
use_threadpool = True | Uses a limited number of threads (10 by default) for this handler. Each thread handles one request at a time.
threadpool_kill_thread_limit = 10800 | The number of seconds a thread can run before being killed automatically (3 hours)
]

---

### Re-run your instance in daemon mode

If your Galaxy instance is still running, stop it with the `Ctrl + c` key sequence

```shell
[galaxy]$ GALAXY_RUN_ALL=1 ./run.sh --daemon
```

Your Galaxy instance now works with two handlers: one on port `8080` and one on` 8090` port

--

### Check your logs

```shell
[galaxy]$ tail -f main.log
[galaxy]$ tail -f job.log
```

Two Galaxy servers have been launched and you can access each of them using the corresponding port. Both servers have the same view of your data or jobs.

---

### Create a job configuration file using the basic sample provided by Galaxy

```shell
[galaxy]$ cp config/job_conf.xml.sample_basic config/job_conf.xml
```

---

### Declare a new handler as a job handler based on the local job launcher

Edit your `job_conf.xml` file as follows :

```xml
<?xml version="1.0"?>
<job_conf>
    <plugins>
        <plugin id="local" type="runner" load="galaxy.jobs.runners.local:LocalJobRunner" workers="1"/>
    </plugins>
    <handlers>
        <handler id="job"/>
    </handlers>
    <destinations>
        <destination id="local" runner="local"/>
    </destinations>
</job_conf>
```

---

### Activate your job configuration in your `galaxy.ini` file

```ini
job_config_file = config/job_conf.xml
```

--

### Re-launch your Galaxy instance

```shell
[galaxy]$ GALAXY_RUN_ALL=1 ./run.sh --stop-daemon
[galaxy]$ GALAXY_RUN_ALL=1 ./run.sh --daemon
```

---

### Re-run the **Change case** tool on your Galaxy instance and check which handler is running the job

.center[![Re-run the Change case tool](images/rerun-job.png)]

```shell
[galaxy]$ tail -f job.log
```

---
layout: true
template: content

# Galaxy Handlers
## Jobs configuration

---

The `job_conf.xml` file is used to delegate the execution of tools to third-party systems (remote servers, clusters, etc.).

.callout.callout-danger[
If you want to use a third-party system to run your tools you must ensure that the access to binaries and datasets is identical on the Galaxy server (the handlers) and on the servers performing the execution of your tools (the runners)
]

.center[![Shared filesystem](images/shared-fs.png)]

---

The `job_conf.xml` file has 5 main sections:
- The plugins
- The handlers
- The destinations
- The tools
- The limits

---

### Plugins

The `<plugins>` section allows you to declare the different types of runner you want to use.

- **CLI** : Launching a command line on a shell (ssh)
- **Condor - Torque/PBS - SLURM** : Direct support for some job scheduler
- **DRMA API** : Distributed Resource Management Application API that support even more job scheduler (Condor, Slurm, Grid Engine, Torque/PBS...)
- **Local** : Execute tools locally on the server running the job handler
- **Pulsar** (formerly LWR) : Execute tools on a remote machine (including Windows) without the need for a shared file system

---

### Plugins

Example:

```xml
<plugins>
    <plugin id="local" type="runner" load="galaxy.jobs.runners.local:LocalJobRunner"/>
    <plugin id="drmaa" type="runner" load="galaxy.jobs.runners.drmaa:DRMAAJobRunner">
        <param id="drmaa_library_path">/sge/lib/libdrmaa.so</param>
    </plugin>
</plugins>
```

---

### Handlers

The `<handlers>` section allows you to declare the Galaxy handlers that will be responsible for forwarding jobs to `runners`.

Each `<handler>` is defined by:
- **id**: an identifier corresponding to the name declared in the `galaxy.ini` file for this handler (`[server: id]`).
- **tag**: This optional attribute is used to group a set of similar handlers.

Example:

```xml
<handlers>
  <handler id="job0" tags="simple"/>
  <handler id="job1" tags="simple"/>
  <handler id="job1" tags="advanced"/>
</handlers>
```

---

### Handlers

By default, each handler loads all the plugins declared in the `<plugins>` section.

It is possible to limit the plugins loaded for a handler thanks to the `<plugin>` tag.

Example :

```xml
<handler id="job3" tags="advanced">
	<plugin id="slurm">
</handler>
```
---

### Les destinations

Les destinations permettent de définir différents paramétrages pour les runners que l’on souhaite utiliser.
Chaque destination correspond à un paramétrage pour un runner donné.

Une <destination> est défini par :
- **id** : l’identifiant unique de la destination
- **runner** : l’identifiant du plugin sur lequel elle s’appuie

Elle contient les paramètres spécifiques au runner utilisé.

Il est possible de regrouper un ensemble de destination par l’intermédiaire de l’attribut **tags**.

---

### Les destinations

Exemple :

```xml
<destinations>
  <destination id="sge" runner="drmaa">
    <param id="nativeSpecification">-q galaxy</param>
  </destination>
  <destination id="smallmem" runner="slurm">
    <param id="nativeSpecification">-q galaxy --mem-per-cpu=512</param>
    <resubmit condition="memory_limit_reached" destination="bigmem" />
  </destination>
</destinations>
```

---

### Les tools

Les handlers et les destinations sont définis par défaut pour l’ensemble des outils.

La section `<tools>` permet de définir des choix spécifiques de destinations et de handlers pour certains outils.

Un `<tool>` est défini par  :
- **id** : l’identifiant de l’outil auquel il fait référence
- **handler** : l’identifiant ou le tag des handlers à utiliser
- **destination** : l’identifiant ou le tag de la destination  

Exemple :

```xml
<tool id="baz" handler="special_handlers" destination="bigmem"/>
```

---

### Les limits

La section `<limits>` permet de définir des limitations concernant l’exécutions des jobs afin de mieux gérer la disponibilité des ressources.

Chaque `<limit>` s’applique pour un **id** destination donné ou un ensemble de destinations portant un tag donné.

Elle cible un `type` de limitation :
- `registered_user_concurrent_jobs`
- `unregistered_user_concurrent_jobs`
- `job_walltime`
- `output_size`
- `concurrent_jobs`

---

### Les limits

Exemples :

```xml
<limits>
  <limit type="registered_user_concurrent_jobs">2</limit>
  <limit type="anonymous_user_concurrent_jobs">1</limit>
  <limit type="destination_total_concurrent_jobs" id="local">16</limit>
  <limit type="output_size">10GB</limit>
</limits>
```
---

template: title

# Scalling

---

## Scalling
### Nginx



---

## Scalling
### uWSGI



---

## Scalling
### Supervisor



---
