class: middle

# Galaxy Installation

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

# Please interrupt!

Your questions will be answered

# Set your post-it!

**Post-it on** = Still working hard on it<br/>
**Post-it removed** = I did it!

---

# Reasons to install your own Galaxy

You only need to download Galaxy if you plan to:

* Run a local production Galaxy because you want to
  * Install and use tools unavailable on public Galaxies
  * Use sensitive data (e.g. clinical)
  * Process large datasets that are too big for public Galaxies
  * Plug-in new datasources
* Develop Galaxy tools
* Develop Galaxy itself

---

# Requirements

* Any Linux or macOS
* Python 2.7

Optional

* samtools (metadata etc.)
* Git code versioning system
* GNU Make + gcc to compile and install tool dependencies
* Additional requirements for shipped tools

---

# Clone the repository

.callout.callout-warning[
#### Warning
The source code of Galaxy is already cloned on your VM in `/usr/local/galaxy/galaxy`
]

The most common way to install Galaxy is to download the source code of the project using Git.

1. check what is the latest [release](https://docs.galaxyproject.org/en/master/releases)
1. run
```shell
$ git clone -b release_16.07 https://github.com/galaxyproject/galaxy.git
```
Release is defined by the branch name : release_16.07

Without specifying branch during clone you are running the *development* version of Galaxy.

---

# Start Galaxy

1. `$ cd galaxy`
1. `[galaxy]$ sh run.sh`
1. Visit `http://localhost:8080`

You should see default Galaxy running.

.callout.callout-warning[
The first startup needs internet connection and takes longer than the subsequent ones.
]

---

# What happened?

* Galaxy created a Python virtual environment in `.venv/` ;
* Galaxy sourced this environment (`$ source .venv/bin/activate`) ;
* Galaxy fetched neeed Python binaries (wheels) into this environment ;
* Galaxy created the default SQLite database and migrated it to the lastest version ;
* Galaxy binded to the default port 8080 on localhost.

.callout.callout-success[
All of the above can be configured.
]

---

# Run a basic job

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

# Check your Galaxy version

Check `http://localhost:8080/api/version` to see Galaxy's version

---

# Basic configuration

* Galaxy works out of the box with default configuration
* Most important config files are in `config/`
* Galaxy often uses the file with suffix `*.sample` as declared defaults

---

# Make your own config

* Stop Galaxy by terminating the console process (Ctrl + c)

* Copy the provided sample
```shell
$ cp config/galaxy.ini.sample config/galaxy.ini
```

* Edit your config file and set the following entries
```ini
message_box_visible = True
message_box_content = European Galaxy Developer Workshop is so fun !
message_box_class = info
```

* (Re)start Galaxy

---

# Update the welcome page

Welcome page is `$GALAXY_ROOT/static/welcome.html`and is the first thing that users see.<br/>
It is a good idea to extend it with thing like :

* Downtimes/Maintenace periods
* New tools
* Publications relating to your Galaxy

No restarting is necessary.

---

# Give a name to your Galaxy instance

* Edit your config file and set the following entries

```ini
brand = My awesome instance
```

* (Re)start Galaxy

---

# Make yourself an administrator

* Create a user using Galaxy interface
* Modify `galaxy.ini` to include `admin_users = your@ema.il`
* (Re)start Galaxy

---

# What to do next ?

* Keep your code up to date
* Install tools
* Join the mailing list
* Configure for production
