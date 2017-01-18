class: middle

# (Proxy) Web Server Choices and Configuration

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

# Reverse Proxy

What is a reverse proxy?
- Sits between the client and Galaxy

Extra features:
- Serve static content
- Compress selected content
- Serve over HTTPS
- Serve byte range requests
- Serve other sites from the same server
- Can provide authentication

Some of these features are available directly in uWSGI.

---

template: content

# Target architecture

.center.image[![Target architecture](images/apache-proxy.png)]

---

template: content

# Web server
## Apache

- The most popular web server
- Many authentication plugins written for Apache
- Can offload file downloads

## NGiNX

- Designed specifically to be a load balancing reverse proxy
- Widely used by large sites (third most popular web server)
- Can offload both uploads and downloads

<br/>
**I recommend NGiNX unless you have a specific need for Apache**

---

template: content

# NGiNX "flavors"

NGiNX plugins must be compiled in<sup>[1]</sup>

Debian/Ubuntu provide multiple nginx "flavors":
- `nginx-light`: minimal set of core modules
- `nginx-full`: full set of core modules
- `nginx-extras`: full set of core modules and extras (3rd party modules)

There is also a "Galaxy" flavor (includes [upload module](https://github.com/vkholodkov/nginx-upload-module)):
- [RHEL](https://depot.galaxyproject.org/yum/) (derived from EPEL nginx)
- [Ubuntu PPA](https://launchpad.net/~galaxyproject/+archive/ubuntu/nginx)

.footnote[<sup>[1]</sup> Dynamic shared objects were [introduced in 1.9.11](https://www.nginx.com/blog/dynamic-modules-nginx-1-9-11/)]

---

template: title

# Hands-on !

## Set up NGiNX as a Reverse Proxy for Galaxy

---

template: hands-on

# Comment the default configuration

* Stop your galaxy instance

```shell
$ GALAXY_RUN_ALL=1 ./run.sh --stop-daemon
```

* Comment the default web handler sections

Edit the `galaxy.ini` file and comment the section `[server:main]`

```ini
# [server:main]
# use = egg:Paste#http
# use_threadpool = True
# threadpool_kill_thread_limit = 10800
```

---

# Add two web handlers

* Edit the `galaxy.ini` file and add two new web handler sections

```ini
[server:web1]
use = egg:Paste#http
port = 8081
host = 127.0.0.1
use_threadpool = true
threadpool_kill_thread_limit = 10800

[server:web2]
use = egg:Paste#http
port = 8082
host = 127.0.0.1
use_threadpool = true
threadpool_kill_thread_limit = 10800
```

---

# Apply changes

* Restart Galaxy

```shell
$ GALAXY_RUN_ALL=1 ./run.sh --daemon
Handling web1 with log file web1.log...
Entering daemon mode
Handling web2 with log file web2.log...
Entering daemon mode
```

* Check the log files

```shell
$ tail -2 web2.log
Starting server in PID 5343.
serving on http://127.0.0.1:8082
```

---

# Stop Apache

```shell
$ sudo service httpd stop
```

---

# Install NGiNX

```shell
$ sudo yum install nginx
```

---

# Disable NGiNX default config

```shell
$ sudo mv /etc/nginx/conf.d/default.conf /etc/nginx/conf.d/default.conf.disabled
```

# Create a NGiNX configuration
* Create a new virtual host file for Galaxy in your Nginx configuration
```shell
$ sudo vim /etc/nginx/conf.d/galaxy.conf
```

<br/>
(see next slide)

---

# Create a NGiNX configuration
```
upstream galaxy {
    server localhost:8081;
    server localhost:8082;
}


server {
    listen 80;

    client_max_body_size 500M;

    location / {
        proxy_pass   http://galaxy;
        proxy_set_header   X-Forwarded-Host $host;
        proxy_set_header   X-Forwarded-For  $proxy_add_x_forwarded_for;
        proxy_buffer_size  128k;
        proxy_buffers      4 256k;
        proxy_busy_buffers_size 256k;
    }
}
```

---
# Start NGiNX

```shell
$ sudo service nginx start
```

* Access your Galaxy instance on http://localhost
