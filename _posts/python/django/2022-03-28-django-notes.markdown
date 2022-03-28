---
layout: post
title:  "Django Notes"
categories: django
---

Here are notes taken from Django official tutorials.

### Creating a Project
```
django-admin startproject <project-name>
```
### Initial Project files.
Sample structures:
```
mysite/
    manage.py
    mysite/
        __init__.py
        settings.py
        urls.py
        asgi.py
        wsgi.py
```
* **manage.py**: A command-line utility that lets you manage the project, in various ways.
* The inner **mysite/**: The actual Python package for your project.
* **__init__.py**: Normally empty, just indicates the directory is a package.
* **settings.py**: Settings of this Django project/package.
* **urls.y**: The URL declarations of your Django project.
* **asgi.py**: Entry-point for ASGI-compatible web servers to serve your project.
* **wsgi.py**: Entry-point for WSGI-compatible web servers to serve your project.

### Start the Server (development)
```
python manage.py runserver [[ip:]port]
```
Note: don't use this development server in anything resembling a production environment. It's intended only for use whiling developing.

