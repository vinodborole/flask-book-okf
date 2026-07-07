---
type: Web Page
title: uWSGI — Flask Documentation (3.1.x)
resource: https://flask.palletsprojects.com/en/stable/deploying/uwsgi
timestamp: '2026-07-07T08:53:11.212445+00:00'
---

# uWSGI

uWSGI is a fast, compiled server suite with extensive configuration and capabilities beyond a basic server.

- It can be very performant due to being a compiled program. 
- It is complex to configure beyond the basic application, and has so many options that it can be difficult for beginners to understand. 
- It does not support Windows (but does run on WSL). 
- It requires a compiler to install in some cases. 

This page outlines the basics of running uWSGI. Be sure to read its documentation to understand what features are available.

## Installing

uWSGI has multiple ways to install it. The most straightforward is to
install the `pyuwsgi` package, which provides precompiled wheels for
common platforms. However, it does not provide SSL support, which can be
provided with a reverse proxy instead.

Create a virtualenv, install your application, then install `pyuwsgi`.

```
$ cd hello-app
$ python -m venv .venv
$ . .venv/bin/activate
$ pip install .  # install your application
$ pip install pyuwsgi
```
If you have a compiler available, you can install the `uwsgi` package
instead. Or install the `pyuwsgi` package from sdist instead of wheel.
Either method will include SSL support.

```
$ pip install uwsgi
# or
$ pip install --no-binary pyuwsgi pyuwsgi
```
## Running

The most basic way to run uWSGI is to tell it to start an HTTP server and import your application.

```
$ uwsgi --http 127.0.0.1:8000 --master -p 4 -w hello:app
*** Starting uWSGI 2.0.20 (64bit) on [x] ***
*** Operational MODE: preforking ***
mounting hello:app on /
spawned uWSGI master process (pid: x)
spawned uWSGI worker 1 (pid: x, cores: 1)
spawned uWSGI worker 2 (pid: x, cores: 1)
spawned uWSGI worker 3 (pid: x, cores: 1)
spawned uWSGI worker 4 (pid: x, cores: 1)
spawned uWSGI http 1 (pid: x)
```
If you’re using the app factory pattern, you’ll need to create a small Python file to create the app, then point uWSGI at that.

```
from hello import create_app
app = create_app()
```
```
$ uwsgi --http 127.0.0.1:8000 --master -p 4 -w wsgi:app
```
The `--http` option starts an HTTP server at 127.0.0.1 port 8000. The
`--master` option specifies the standard worker manager. The `-p`
option starts 4 worker processes; a starting value could be `CPU * 2`.
The `-w` option tells uWSGI how to import your application

## Binding Externally

uWSGI should not be run as root with the configuration shown in this doc because it would cause your application code to run as root, which is not secure. However, this means it will not be possible to bind to port 80 or 443. Instead, a reverse proxy such as nginx or Apache httpd should be used in front of uWSGI. It is possible to run uWSGI as root securely, but that is beyond the scope of this doc.

uWSGI has optimized integration with Nginx uWSGI and Apache mod_proxy_uwsgi, and possibly other servers, instead of using a standard HTTP proxy. That configuration is beyond the scope of this doc, see the links for more information.

You can bind to all external IPs on a non-privileged port using the
`--http 0.0.0.0:8000` option. Don’t do this when using a reverse proxy
setup, otherwise it will be possible to bypass the proxy.

```
$ uwsgi --http 0.0.0.0:8000 --master -p 4 -w wsgi:app
```
`0.0.0.0` is not a valid address to navigate to, you’d use a specific
IP address in your browser.

## Async with gevent

The default sync worker is appropriate for most use cases. If you need numerous,
long running, concurrent connections, uWSGI provides an asynchronous worker
using gevent. This is not the same as Python’s `async/await`, or the ASGI
server spec. See Async with Gevent for more information about enabling it in your
application.

When using gevent, greenlet>=1.0 is required. When using PyPy, PyPy>=7.3.7 is required.

```
$ uwsgi --http 127.0.0.1:8000 --master --gevent 100 -w wsgi:app
*** Starting uWSGI 2.0.20 (64bit) on [x] ***
*** Operational MODE: async ***
mounting hello:app on /
spawned uWSGI master process (pid: x)
spawned uWSGI worker 1 (pid: x, cores: 100)
spawned uWSGI http 1 (pid: x)
*** running gevent loop engine [addr:x] ***
```

# Citations

1. Source page: https://flask.palletsprojects.com/en/stable/deploying/uwsgi
