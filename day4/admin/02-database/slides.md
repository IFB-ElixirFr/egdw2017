class: middle

# Galactic Database

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

layout: true
name: hands-on

.footer[
EGDW 2017 - **Hands-on**
]

---

template: content

# Please interrupt!

Your questions will be answered

# Set your post-it!

**Post-it on** = Still working hard on it<br/>
**Post-it removed** = I did it!

---

# Defaults

* Galaxy uses database abstraction layer SQLAlchemy. This allows for different databases engine to be plugged in.
* By default, Galaxy will automatically create and use a SQLite database during first startup.

*The database is in file `database/universe.sqlite`*

---

# Choices

* SQLite<br/>
Useful for ad-hoc Galaxies or development.

* **PostgreSQL**<br/>
The recommended standard for anything serious.

* ~~MySQL~~<br/>
Supported in past but Galaxy is not tested against it anymore.

---

# Configuration

`database_connection` is specified as a connection string in `galaxy.ini` file.

* default SQLite : `sqlite:///./database/universe.sqlite?isolation_level=IMMEDIATE`
* local PostgreSQL : `postgres://<name>:<password>@localhost:5432/galaxy`

---

template: title

# Hands-on !

## Connecting Galaxy to PostgreSQL



---

template: hands-on

# Add PostgreSQL user and database

* Create a PostgreSQL user for Galaxy

```shell
$ sudo -H -u postgres createuser galaxyguest
```

*You can answer No (n) to all rights questions*

* Create a PostgreSQL database for Galaxy

```shell
$ sudo -H -u postgres createdb -O galaxyguest galaxyguest
```

---

# Configure Galaxy

* Open up the Galaxy config file, `galaxy.ini`, in an editor and find the line:

```ini
#database_connection = sqlite:///./database/universe.sqlite?isolation_level=IMMEDIATE
```

* Uncomment it and change it to :

```ini
database_connection = postgresql://galaxyguest:galaxyguest@locahost/galaxyguest
```

* (Re)start Galaxy

---

template: content

# Tuning

* **pool**<br/>
set `pool_size` and `max_overflow` if galaxy logs error about not having enough database pool connection
* **server cursor**<br/>
turn on `server_side_cursors` if large database query reulsts are causing memory or response time issues
* **install database**<br/>
Galaxy can track Tool Shed data in a separate DB
---

# Migration

The changes in DB model are captured incrementally in form of [atomic scripts](https://github.com/galaxyproject/galaxy/tree/dev/lib/galaxy/model/migrate/versions).

Each script can both upgrade and downgrade a DB.

```shell
$ bash manage_db.sh upgrade
$ bash manage_db.sh downgrade --version=132
```
