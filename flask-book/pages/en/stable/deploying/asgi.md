---
type: Web Page
title: ASGI — Flask Documentation (3.1.x)
resource: https://flask.palletsprojects.com/en/stable/deploying/asgi
timestamp: '2026-07-09T12:16:47.677177+00:00'
---

# ASGI

If you’d like to use an ASGI server you will need to utilise WSGI to
ASGI middleware. The asgiref
[WsgiToAsgi](https://github.com/django/asgiref#wsgi-to-asgi-adapter)
adapter is recommended as it integrates with the event loop used for
Flask’s [Using async and await](../../async-await/#async-await) support. You can use the adapter by
wrapping the Flask app,

```
from asgiref.wsgi import WsgiToAsgi
from flask import Flask
app = Flask(__name__)
...
asgi_app = WsgiToAsgi(app)
```
and then serving the `asgi_app` with the ASGI server, e.g. using
[Hypercorn](https://github.com/pgjones/hypercorn),

```
$ hypercorn module:asgi_app
```

# Citations

1. Source page: https://flask.palletsprojects.com/en/stable/deploying/asgi
