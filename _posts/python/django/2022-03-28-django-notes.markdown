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

### Create an app.
```
python manage.py startapp <appname>
```
The example result of creating app *polls*:
```
polls/
    __init__.py
    admin.py
    apps.py
    migrations/
        __init__.py
    models.py
    tests.py
    views.py
```

### Write first view
In **polls/views.py**, we add:
```python
from django.http import HttpResponse

def index(request):
    return HttpResponse("Hello, world. You're at the polls index.")
```
To call the view, we need to map it to a URL. We can create file **urls.py**, and put some codes in it:
```python
from django.urls import path
from . import views

urlpatterns = [
    path('', views.index, name='index'),
]
```

The next step is point the root URLconf at the **polls.url** module. In **mysite/urls.py**, we add:
```python
from django.contrib import admin
from django.urls import include, path

urlpatterns = [
    path('polls/', include('polls.urls')),
    path('admin/', admin.site.urls),
]
```
The **include()** function allows referencing other URLconfs. Whenever Django encounters **include()**, it chops off whatever part of the URL matched up to that point and sends the remaining string the the included URLconf for further processing.

Note: You should always use **include()** when you include other URL patterns. **admin.site.urls** is the only exception to this.

#### path() argument: route
**route** is a string that contains a URL pattern.

#### path() argument: view
When Django finds a matching pattern, it calls the specified view function with an **HttpRequest** object as the first argument and any "captured" values from the route as keyword arguments.

#### path() argument: kwargs
Arbitrary keyword arguments can be passed in a dictionary to the target view.

#### path() argument: name
Naming your URL lets you refer to it unambiguously from elsewhere in Django.

### Database Setup
These applications(INSTALLED_APPS) may use database, and we need to create it:
```
python manage.py migrate
```
The **migrate** commands looks at **INSTALLED_APPS** setting and creates any necessary database tables according to the database settings in your **settings.py**, and the database migrations shipped with the app.

### Creating models
Philosophy: A model is the single, definitive source of information about your data. It contains the essential fields and behaviours of the data you're storing.

In this example, **Question** and **Choice** models are created in **polls/models.py**:
```python
from django.db import models

class Question(models.Model):
    question_text = models.CharField(max_length=200)
    pub_date = models.DateTimeField('date published')

class Choice(models.Model):
    question = models.ForeignKey(Question, on_delete=models.CASCADE)
    choice_text = models.CharField(max_length=200)
    votes = models.IntegerField(default=0)
```

Each model is represented by a class that subclasses **django.db.models.Model**. Each model has a number of class variables, each of which represents a database field in the model.

Each field is represented by an instance of a **Field** class. This tells Django what type of data each field holds.

You can use an optional first positional argument to **Field** to designate a human-readable name.

Note a relationship is defined using **ForeignKey**.

### Activating models
To include the app in our project, we need to add a reference to its configuration class in the **INSTALLED_APPS** setting. The **PollsConfig** class is in the **polls/apps.py** file, so its dotted path is **polls.apps.PollsConfig**. Add this to **INSTALLED_APPS** in **settings.py**:
```
INSTALLED_APPS = [
    'polls.apps.PollsConfig',
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
]
```

Now Django knows to include the **polls** app. Run following to do the migration:
```
python manage.py makemigrations polls
```

By running **makemigrations**, you're telling Django that you've made some changes to your models, and you'd like the changes to be stored as a *migration*.

Migrations are how Django stores changes to your models and database schema - they are files on disk.

There's a command that will run the migrations for you and manage your database schema automatically - that's called **migrate**. The **sqlmigrate** command takes migration names and returns their SQL:
```
python manage.py sqlmigrate polls 0001
```
And you can see the result SQL statements.

Notes:
* Table names are automatically generated by combining the name of the app and the lowercase name of the model.
* Primary keys(ID) are added automatically. (This can be overrided.)
* By convention, Django appends "\_id" to the foreign key field name.
* It's tailored to the database you're using.
* The **sqlmigrate** command doesn't actually run the migration on your database.

If you're interested, you can also run **python manage.py check** for any problems in your project without making migrations or touching the database.

Now, run **migrate** to apply:
```
python manage.py migrate
```

The **migrate** command takes all migrations that haven't been applied and runs them against your database.

Now remember the three-step guide to making model changes:
* Change your models.
* Run **python manage.py makemigrations** to create migrations for those changes.
* Run **python manage.py migrate** to apply those changes to database.

### Playing with the API
```
python manage.py shell
```
We're using this instead of simply typing "python", because **manage.py** sets the **DJANGO_SETTINGS_MODULE** environment variable, which gives Django the Python import path to your **mysite/settings.py** file.

Once you're in the shell, you can explore the database API in the interactive shell, such as query, add, modify, delete.

Note: It's important to add **__str__()** methods to your models, not only for convenience when dealing with the interactive prompt, but also because objects' representations are used throughout Django's automatically-generated admin.

Samples:
```python
from polls.models import Choice, Question
from django.utils import timezone

Question.objects.all()  # Show all records

q = Question(question_text="What's new?", pub_date=timezone.now())
q.save()
q.id
q.question_text

Question.objects.filter(id=1)  ## Return a QuerySet
Question.objects.filter(question_text__startswith='What')

current_year = timezone.now().year
Question.objects.get(pub_date__year=current_year)

Question.objects.get(id=1)  ## Return the object we can manipulate.
Question.objects.get(pk=1)

## Use Question to manipualte Choice.
q.choice_set.all()
q.choice_set.create(choice_text='Not much', votes=0)
```

### Introducing the Django Admin
#### Creating an admin user
First, we need to create a user who can login to the admin site:
```
python manage.py createsuperuser
```

Then run the server, and open browser to visit **http://127.0.0.1:8000/admin/**.

You should see a few types of editable content: groups and users. They are provided by **django.contrib.auth**.

#### Make the poll app modifiable in the admin
We need to tell the admin that **Question** objects have an admin interface. Open **polls/admin.py**, edit it:
```python
from django.contrib import admin
from .models import Question

admin.site.register(Question)
```

### View overview
A view is a "type" of web page in your Django application that serves a specific function and has a specific template.

In Django, web pages the other content are delivered by views. Each view is represented by a Python function (or method, in the case of class-based views).

To get from a URL to a view, Django uses what are known as 'URLconfs'. A URLconf maps URL patterns to views.

#### Writing more views
Add more views in **polls/views.py**, which takes an argument:
```python
def detail(request, question_id):
    return HttpResponse("You're looking at question %s." % question_id)

def results(request, question_id):
    response = "You're looking at the results of question %s."
    return HttpResponse(response % question_id)

def vote(request, question_id):
    return HttpResponse("You're voting on question %s." % question_id)
```

Then add these views into **polls.urls** module:
```python
from django.urls import path

from . import views

urlpatterns = [
    # ex: /polls/
    path('', views.index, name='index'),
    # ex: /polls/5/
    path('<int:question_id>/', views.detail, name='detail'),
    # ex: /polls/5/results/
    path('<int:question_id>/results/', views.results, name='results'),
    # ex: /polls/5/vote/
    path('<int:question_id>/vote/', views.vote, name='vote'),
]
```

When somebody requests a page from your website - say, "/polls/34/", Django will load the **mysite.urls** Python module because it's pointed to by the **ROOT_URLCONF** settings. It finds the variable named **urlpatterns** and traverses the pattern in order. After finding the match at **'polls/'**, it strips off the matching text and sends the remaining text - **"34"** - to the 'polls.urls' URLconf for further processing. Then it matches **'\<int:question_id\>/'**, resulting in a call to the **detail()** view like so:
```
detail(request=<HttpRequest object>, question_id=34)
```

Using angle brackets "captures" part of the URL and sends it as a keyword argument to the view function. The **question_id** part of the string defines the name that will be used to identify the matched pattern, and the **int** part is a converter that determines what patterns should match this part of the URL path.

#### Write views that actually do something
Each view is responsible for doing one of two things: returning an **HttpResponse** object containing the content for the requested page, or raising an exception such as **Http404**. The rest is up to you.

Change the **index()** view to access database:
```python
from django.http import HttpResponse
from .models import Question

def index(request):
    latest_question_list = Question.objects.order_by('-pub_date')[:5]
    output = ', '.join([q.question_text for q in latest_question_list])
    return HttpResponse(output)
```

We can separate Python and view code by using Django's template system. First, create a directory called **tempaltes** in your **polls** directory.

Your project's **TEMPLATES** setting describes how Django will load and render templates. The default settings file configures a **DjangoTemplates** backend whose **APP_DIRS** option is set to **True**. By convention **DjangoTemplates** looks for a "templates" subdirectory in each of the **INSTALLED_APPS**.

You should create a **polls** directory within **templates** directory. In other words, your template should be at **polls/templates/polls/index.html**. Because of how the **app_directories** template loader works as described above, you can refer to this template within Django as **polls/index.html**.

Put the following code in that template:

{% raw %}
```
{% if latest_question_list %}
    <ul>
    {% for question in latest_question_list %}
        <li><a href="/polls/{{ question.id }}/">{{ question.question_text }}</a></li>
    {% endfor %}
    </ul>
{% else %}
    <p>No polls are available.</p>
{% endif %}
```
{% endraw %}

And modify **polls/view.py**:
```python
from django.http import HttpResponse
from django.template import loader
from .models import Question

def index(request):
    latest_question_list = Question.objects.order_by('-pub_date')[:5]
    template = loader.get_template('polls/index.html')
    context = {
        'latest_question_list': latest_question_list,
    }
    return HttpResponse(template.render(context, request))
```

Above code loads the template called **polls/index.html** and passes it a context. The context is a dictionary mapping template variable names to Python objects.

#### A shortcut: **render()**
It's a very common idiom to load a template, fill a context and return an **HttpResponse** object with the result of the rendered template. Django provides a shortcut:

```python
from django.shortcuts import render
from .models import Question

def index(request):
    latest_question_list = Question.objects.order_by('-pub_date')[:5]
    context = {'latest_question_list': latest_question_list}
    return render(request, 'polls/index.html', context)
```

### Raising a 404 error
Now, let's tackle the question detail view:

```python
# polls/views.py
from django.http import Http404
from django.shortcuts import render
from .models import Question

def detail(request, question_id):
    try:
        question = Question.objects.get(pk=question_id)
    except Question.DoesNotExist:
        raise Http404("Question does not exist")
    return render(request, 'polls/detail.html', {'question': question})
```

#### A shortcut: get_object_or_404()
It's a very common idiom to use **get()** and raise **Http404** if the object doesn't exist. Django provides a shortcut.

```python
# polls/view.py
from django.shortcuts import get_object_or_404, render
from .models import Question

def detail(request, question_id):
    question = get_object_or_404(Question, pk=question_id)
    return render(request, 'polls/detail.html', {'question': question})
```

### Use the template system
Given the context variable **question**, here's what the **polls/detail.html** template might look like:

{% raw %}
```
<h1>{{ question.question_text }}</h1>
<ul>
{% for choice in question.choice_set.all %}
    <li>{{ choice.choice_text }}</li>
{% endfor %}
</ul>
```
{% endraw %}

The template system uses dot-lookup syntax to access variable attributes.

Method-calling happens in the {% raw %}`{% for %}`{% endraw %} loop: **question_choice_set.all** is interpreted as the Python code **question.choice_set.all()**, which returns an iterable of **Choice** objects and is suitable for use in the {% raw %}`{% for %}`{% endraw %} tag.

### Removing hardcoded URLs in templates


