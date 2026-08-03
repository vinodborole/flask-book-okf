---
type: Web Page
title: Subclassing Flask — Flask Documentation (3.1.x)
resource: https://flask.palletsprojects.com/en/stable/patterns/subclassing
timestamp: '2026-08-03T09:38:59.518818+00:00'
---

# Subclassing Flask

The [`Flask`](../../api/#flask.Flask) class is designed for subclassing.

For example, you may want to override how request parameters are handled to preserve their order:

```
from flask import Flask, Request
from werkzeug.datastructures import ImmutableOrderedMultiDict
class MyRequest(Request):
    """Request subclass to override request parameter storage"""
    parameter_storage_class = ImmutableOrderedMultiDict
class MyFlask(Flask):
    """Flask subclass using the custom request class"""
    request_class = MyRequest
```
This is the recommended approach for overriding or augmenting Flask’s internal functionality.

# Citations

1. Source page: https://flask.palletsprojects.com/en/stable/patterns/subclassing
