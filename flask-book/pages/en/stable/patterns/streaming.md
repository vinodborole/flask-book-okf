---
type: Web Page
title: Streaming Contents — Flask Documentation (3.1.x)
resource: https://flask.palletsprojects.com/en/stable/patterns/streaming
timestamp: '2026-07-09T12:16:47.677177+00:00'
---

# Streaming Contents

Sometimes you want to send an enormous amount of data to the client, much more than you want to keep in memory. When you are generating the data on the fly though, how do you send that back to the client without the roundtrip to the filesystem?

The answer is by using generators and direct responses.

## HTTP Response Behavior

**Headers cannot be changed after the streaming response starts.**

When using streaming, it’s important to be aware of the order than an HTTP response is sent. All headers must be sent first, then the body. More headers cannot be sent after the body has begun. Therefore, you must make sure all headers are set before starting the response, outside the generator.

In particular, if the generator will access `session`, be sure to do so in the
view as well so that the `Vary: cookie` header will be set. Do not modify the
session in the generator, as the `Set-Cookie` header will already be sent.

## Basic Usage

This is a basic view function that generates a lot of CSV data on the fly. The trick is to have an inner function that uses a generator to generate data and to then invoke that function and pass it to a response object:

```
@app.route('/large.csv')
def generate_large_csv():
    def generate():
        for row in iter_all_rows():
            yield f"{','.join(row)}\n"
    return generate(), {"Content-Type": "text/csv"}
```
Each `yield` expression is directly sent to the browser.  Note though
that some WSGI middlewares might break streaming, so be careful there in
debug environments with profilers and other things you might have enabled.

## Streaming from Templates

The Jinja template engine supports rendering a template piece by
piece, returning an iterator of strings. Flask provides the
[ stream_template()](../../api/#flask.stream_template) and 

[functions to make this easier to use.](../../api/#flask.stream_template_string)

`stream_template_string()````
from flask import stream_template
@app.get("/timeline")
def timeline():
    return stream_template("timeline.html")
```
The parts yielded by the render stream tend to match statement blocks in the template.

## Streaming with Context

The [ request](../../api/#flask.request) will not be active while the generator is
running, because the view has already returned at that point. If you try
to access 

`request`, you’ll get a `RuntimeError`.If your generator function relies on data in `request`, use the
[ stream_with_context()](../../api/#flask.stream_with_context) wrapper. This will keep the request
context active during the generator.

```
from flask import stream_with_context, request
from markupsafe import escape
@app.route('/stream')
def streamed_response():
    def generate():
        yield '<p>Hello '
        yield escape(request.args['name'])
        yield '!</p>'
    return stream_with_context(generate())
```
It can also be used as a decorator.

```
@stream_with_context
def generate():
    ...
return generate()
```
The [ stream_template()](../../api/#flask.stream_template) and

[functions automatically use](../../api/#flask.stream_template_string)

`stream_template_string()`[if a request is active.](../../api/#flask.stream_with_context)

`stream_with_context()`

# Citations

1. Source page: https://flask.palletsprojects.com/en/stable/patterns/streaming
