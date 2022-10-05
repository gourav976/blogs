
# Python WSGI & ASGI 

Python frameworks are divide into two method which are WSGI and ASGI, Frameworks on the use case can be selected and works accordingly.

## What is WSGI ?

WSGI is the Web Server Gateway Interface. It is a specification that describes how a web server communicates with web applications, and how web applications can be chained together to process one request.
WSGI provided a standard for synchronous Python apps.

### Application Frameworks
- Bottle
- Django
- Flask
- PoorWSGI
- Pyramid
- web.py
- web2py
- and more


###  🔗 Offical Documentation
[Doc Link](https://wsgi.readthedocs.io/)

## What is ASGI ?

ASGI (Asynchronous Server Gateway Interface) is a spiritual successor to WSGI, intended to provide a standard interface between async-capable Python web servers, frameworks, and applications.
Its primary goal is to provide a way to write HTTP/2 and WebSocket code alongside normal HTTP handling code.

### Application Frameworks
- Django Channels
- FastAPI
- Quart
- Sanic
- and more


###  🔗 Offical Documentation
[Doc Link](https://asgi.readthedocs.io/)

## WSGI challenges & ASGI Solutions

### What’s wrong with WSGI?
You may ask “why not just upgrade WSGI”? 

This has been asked many times over the years, and the problem usually ends up being that WSGI’s single-callable interface just isn’t suitable for more involved Web protocols like WebSocket.
WSGI applications are a single, synchronous callable that takes a request and returns a response; this doesn’t allow for long-lived connections, like you get with long-poll HTTP or WebSocket connections.

Even if we made this callable asynchronous, it still only has a single path to provide a request, so protocols that have multiple incoming events (like receiving WebSocket frames) can’t trigger this.

### How does ASGI work?

ASGI is structured as a single, asynchronous callable.

It takes a scope, which is a dict containing details about the specific connection, send, an asynchronous callable, that lets the application send event messages to the client, and receive, an asynchronous callable which lets the application receive event messages from the client.

This not only allows multiple incoming events and outgoing events for each application, but also allows for a background coroutine so the application can do other things (such as listening for events on an external trigger, like a Redis queue).