---
layout: post
title:  "Django Notes 2"
categories: django
---

### Write a minimal form
Let's update poll detail template("polls/detail.html"), so that the template contains an HTML **\<form\>** element:

{% raw %}
```html
<form action="{% url 'polls:vote' question.id %}" method="post">
{% csrf_token %}
<fieldset>
    <legend><h1>{{ question.question_text }}</h1></legend>
    {% if error_message %}<p><strong>{{ error_message }}</strong></p>{% endif %}
    {% for choice in question.choice_set.all %}
        <input type="radio" name="choice" id="choice{{ forloop.counter }}" value="{{ choice.id }}">
        <label for="choice{{ forloop.counter }}">{{ choice.choice_text }}</label><br>
    {% endfor %}
</fieldset>
<input type="submit" value="Vote">
</form>
```
{% endraw %}

A quick rundown:
* The above template displays a radio button for each question choice. The **value** of each radio button is the question choice's ID. The **name** of each radio button is **choice**. That means, when somebody selects one of the radio buttons and submit the form, it will send the POST data **choice=\#** where \# is the ID of the selected choice. This is the basic concept of HTML forms.
* We set the form's **action** to {% raw %}**{% url 'polls:vote' question.id %}**{% endraw %}, and we set **method="post"**. Using **POST** (instead of GET) is very important, because the act of submitting this form will alter data server-side.
* **forloop.counter** indicates how many times the **for** tag has gone through its loop.
* Since we're creating a POST form, we need to worry about Cross Site Request Forgeries. Django comes with a helpful system for protecting against it, by using the {%raw%}**{% csrf_token %}**{%endraw%} template tag.

Add the following to **polls/views.py** to handle the request:

{%raw%}
```python
from django.http import HttpResponse, HttpResponseRedirect
from django.shortcuts import get_object_or_404, render
from django.urls import reverse

from .models import Choice, Question
# ...
def vote(request, question_id):
    question = get_object_or_404(Question, pk=question_id)
    try:
        selected_choice = question.choice_set.get(pk=request.POST['choice'])
    except (KeyError, Choice.DoesNotExist):
        # Redisplay the question voting form.
        return render(request, 'polls/detail.html', {
            'question': question,
            'error_message': "You didn't select a choice.",
        })
    else:
        selected_choice.votes += 1
        selected_choice.save()
        # Always return an HttpResponseRedirect after successfully dealing
        # with POST data. This prevents data from being posted twice if a
        # user hits the Back button.
        return HttpResponseRedirect(reverse('polls:results', args=(question.id,)))
```
{%endraw%}

A quick rundown:
* **request.POST** is a dictionary-like object that lets you access submitted data by key name. **request.POST** values are always strings.
* **request.POST['choice']** will raise **KeyError** if **choice** wasn't provided in POST data.
* After incrementing the choice count, the code returns an **HttpResponseRedirect** rather than a normal **HttpResponse**. **HttpResponseRedirect** takes a single argument: the URL to which the user will be redirected. You should always return an **HttpResponseRedirect** after successfully dealing with POST data.
* **reverse()** function helps avoid having to hardcode a URL in the view function. It is given the name of the view that we want to pass control to and the variable portion of the URL pattern that points to that view. In this case, using the URLconf we set up before, this **reverse()** call will return a string like `'/polls/3/results'` where **3** is the value of **question.id**.

And view **results**:

{%raw%}
```html
def results(request, question_id):
    question = get_object_or_404(Question, pk=question_id)
    return render(request, 'polls/results.html', {'question': question})
```
{%endraw%}

And create a **polls/tempaltes/polls/results.html**:

{%raw%}
```html
<h1>{{ question.question_text }}</h1>

<ul>
{% for choice in question.choice_set.all %}
    <li>{{ choice.choice_text }} -- {{ choice.votes }} vote{{ choice.votes|pluralize }}</li>
{% endfor %}
</ul>

<a href="{% url 'polls:detail' question.id %}">Vote again?</a>
```
{%endraw%}

Note: There is some concurrency problem for incrementing **votes** variable. (Read-Add-Save)

### Use generic views: Less code is better
These views represent a common case of basic web development: getting data from the database according to a parameter passed in the URL, loading a template and returning the rendered template. Because this is so common, Django provides a shortcut, called **generic view** system.

#### Amend URLconf
Change **polls/urls.py** URLconf to:

```python
from django.urls import path

from . import views

app_name = 'polls'
urlpatterns = [
    path('', views.IndexView.as_view(), name='index'),
    path('<int:pk>/', views.DetailView.as_view(), name='detail'),
    path('<int:pk>/results/', views.ResultsView.as_view(), name='results'),
    path('<int:question_id>/vote/', views.vote, name='vote'),
]
```

Note that the name of the matched pattern in the path string of second and third patterns has changed from **\<question_id\>** to **\<pk\>**.

#### Amend views
Next, remove old **index**, **detail**, **results** view and use generic views instead. Edit **polls/views.py**:

```python
from django.http import HttpResponseRedirect
from django.shortcuts import get_object_or_404, render
from django.urls import reverse
from django.views import generic
from .models import Choice, Question

class IndexView(generic.ListView):
    template_name = 'polls/index.html'
    context_object_name = 'latest_question_list'

    def get_queryset(self):
        """Return the last five published questions."""
        return Question.objects.order_by('-pub_date')[:5]

class DetailView(generic.DetailView):
    model = Question
    template_name = 'polls/detail.html'

class ResultsView(generic.DetailView):
    model = Question
    template_name = 'polls/results.html'
```

We're using two generic views here: **ListView** and **DetailView**. Respectively, those two views abstract the concepts of "displaying a list of objects" and "displaying a detailed page for a particular type of object".

* Each generic view needs to know what model it will be acting upon. This is provided by **model** attribute.
* The **DetailView** generic view expects the primary key captured from the URL to be called **pk**.

The **template_name** attribute is used to tell Django to use a specific template name instead of the autogenerated default one.

For **DetailView**, the **question** variable is provided automatically. However, for **ListView**, the automatically generated context variable is **question_list**. To override this we provide **context_object_name** attribute, specifying that we want to use **latest_question_list** instead.

### Automatic Test
A conventional place for an application's tests is in the application's **tests.py** file; the testing system will automatically find tests in any file whose name begins with test. Add to **tests.py**:

```python
import datetime
from django.test import TestCase
from django.utils import timezone
from .models import Question

class QuestionModelTests(TestCase):

    def test_was_published_recently_with_future_question(self):
        """
        was_published_recently() returns False for questions whose pub_date
        is in the future.
        """
        time = timezone.now() + datetime.timedelta(days=30)
        future_question = Question(pub_date=time)
        self.assertIs(future_question.was_published_recently(), False)
```

#### Running tests
```
python manage.py test polls
```

What happened is this:
* it found a subclass of the **django.test.TestCase** class.
* it created a special database for the purpose of testing.
* it looked for test methods - ones whose names begin with **test**.

For more test tutorial, visit [Django Test tutorial](https://docs.djangoproject.com/en/4.0/intro/tutorial05/).

### Static files
For small projects, this isn't a big deal, because you can keep static files somewhere your web server can find it. However, in bigger projects - especially those comprised of multiple apps - dealing with the multiple sets of static files provided by each application starts to get tricky.

That's what **django.contrib.staticfiles** is for: it collects static files from each of your applications into a single location that can be easily served for production.

For more information, refer to [Offical static files tutorial](https://docs.djangoproject.com/en/4.0/intro/tutorial06/).

