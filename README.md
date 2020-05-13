# User Registration and User Authentication

- This document explains the usage of Django’s authentication system in its default configuration. This configuration has evolved to serve the most common project needs, handling a reasonably wide range of tasks, and has a careful implementation of passwords and permissions.
- Django authentication provides both authentication and authorization together and is generally referred to as the authentication system, as these features are somewhat coupled.

### User objects
User objects are the core of the authentication system. They typically represent the people interacting with your site and are used to enable things like restricting access, registering user profiles, associating content with creators etc. Only one class of user exists in Django’s authentication framework, i.e., 'superusers' or admin 'staff' users are just user objects with special attributes set, not different classes of user objects.

The primary attributes of the default user are:
- username
- first_name
- last_name
- email
- password1
- password2

# Creating a Django Project & Application

- After installing Django, you need to create a project using django.

## Project Create
```
  django-admin startproject studentmanagement
```
- Next, create an application using manage.py, you can name it profiles

## App Create
```
  django-admin startapp profiles
```
> **_NOTE:_** Don't forget to mention app name in INSTALLED_APPS list of settings.py file, if you created a new app

```python
INSTALLED_APPS = 
[
    'profiles',(appname)
         ]
```
### Forms     
First we need to create **froms.py** file in our app location

**`forms.py`**
```python
from django.contrib.auth.forms import UserCreationForm
from django.contrib.auth.models import User

class RegisterForm(UserCreationForm):
	class Meta:
		model = User
		fields = ['username','first_name','last_name','email','password1','password2']
```
We need to import Django forms first (from django.contrib.auth.forms import UserCreationForm and from django.contrib.auth.models import User). Next, we have class Meta. Finally, we can say which field(s) should end up in our form. In this scenario if we want only few fields then metion them in a list formate.

### Sync With database

Now that we’ve created the form, it’s time to add it to the database. To do this we need to open Command Promnt and run these two commands: 
-	python manage.py makemigrations 
-	python manage.py migrate
Your terminal output should look something like this:

<img src="docmi.JPG">

### Views
**`views.py`**
```python
from django.shortcuts import render

# Create your views here.
from django.http import HttpResponse,HttpResponseRedirect
from django.urls import reverse
from profiles.forms import RegisterForm

def home(request):
	return render(request,'profiles/index.html')
```

Initially we are using get method, To check how from is working, Here we are using BootstrapCDN
> **_NOTE:_** Add the {% csrf_token %} to every Django template you create that uses POST to submit data. This will reduce the chance of forms being hijacked by malicious users.

### Templates

**`header.html`**
```html
<!DOCTYPE html>
<html>
<head>
	<meta charset="utf-8">
	<meta name="viewport" content="width=device-width, initial-scale=1">
	<link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/4.4.1/css/bootstrap.min.css">
	<script src="https://ajax.googleapis.com/ajax/libs/jquery/3.5.1/jquery.min.js"></script>
	<script src="https://cdnjs.cloudflare.com/ajax/libs/popper.js/1.16.0/umd/popper.min.js"></script>
	<script src="https://maxcdn.bootstrapcdn.com/bootstrap/4.4.1/js/bootstrap.min.js"></script>

	<title> {% block title %} {% endblock %} </title>
</head>
<body style="padding-top: 100px;">
	{% include 'profiles/nav.html' %}
	<div class="container text-left" style="padding-top: 100px;">
		{% block content %}
		{% endblock %}
	</div>
	
</body>
</html>
```

**`nav.html`**

```html
<nav class="navbar navbar-expand-sm bg-dark navbar-dark fixed-top ">
	<li class="nav-item mr-auto">

		<a href="{% url 'profiles:home' %}" class="navbar-brand">Home 
		</a>
	</li>

	<li class="nav-item mr-auto">
		<a href="#" class="navbar-brand"> 
			<span class="title"> student management </span> 
		</a>
	</li>
  	<li class="nav-item">
  		{% if request.user.is_authenticated %}
  			<a href="#" class="navbar-brand">{{request.user.username}}</a>
  			<a href="{% url 'profiles:logout' %}" class="navbar-brand">Logout</a>
  		{% else %}
  			<a href="{% url 'profiles:login' %}" class="navbar-brand">Login</a>
  			<a href="{% url 'profiles:register' %}" class="navbar-brand">Register</a>
  		{% endif %}
	</li>
				  
</nav>

```
**`index.html`**

```html
{% extends 'profiles/header.html' %}
{% comment %}
	<h1>welcome {{request.user.email}} </h1>
	{% if request.user.is_authenticated %}
		<a href="{% url 'profiles:logout' %}">Logout</a>
	{% else %}
		<a href="{% url 'profiles:login' %}">Login</a>
		<a href="{% url 'profiles:register' %}">Register</a>
	{% endif %}
{% endcomment %}
 {{ form.errors }} {{ form.non_field_errors }}

{% block title %}
	Homepage
{% endblock %}

{% block content %}
	<h2>Hello home</h2>
{% endblock %}
```

### Urls

Now our task is to add project urls(studentmanagement).

**`studentmanagement(project)/urls.py`**
```python
from django.contrib import admin
from django.urls import path,include

urlpatterns = [
    path('admin/', admin.site.urls),
    path('student/',include('profiles.urls'))
]
```
add application urls(profiles).

**`profiles(app)/urls.py`**
```python
from django.contrib.auth import views as auth_views
from django.urls import path
from . import views
app_name = 'profiles'
urlpatterns = [
	path('',views.home,name="home"),
	path('register/',views.register,name="register"),
	path('login/',auth_views.LoginView.as_view(template_name = 'profiles/login.html'),name="login"),
	path('logout/',auth_views.LogoutView.as_view(template_name='profiles/logout.html'),name="logout")
]
```

Now run this project **manage.py** location open command prompt(cmd).

```
	python manage.py runserver
```
Now open browser and type url path
```
	https:localhost:8000/student
```

**`output:`**
<img src ="index.JPG">



### Form validation
Till now we haven't used post method from register.html, a visitor will hit the `submit` button after filling up the details, that means the form method is changed to "POST".

Now our task is to validate the form and save details, for that we need to change register function in views.py 

**`views.py`**
``` python
def register(request):
	if request.method == 'POST':
		form = RegisterForm(request.POST)
		if form.is_valid():
			form.save()
			return HttpResponseRedirect(reverse('profiles:home'))
		else:
			return HttpResponse("Invalid")
	form = RegisterForm()
	context = {'form':form}
	return render(request,'profiles/register.html',context)
```
is_valid() validates the form details given by visitor

**`register.html`**

```html
{% extends 'profiles/header.html' %}
{% block content %}
			<form action="{% url 'profiles:register' %}" method="POST">
				<center><h1>Registration Form</h1></center>
				{% csrf_token %}
				{{form.as_p}}
				<input type="submit" class="btn btn-primary" value="signup"  name="submit">
			</form>
		
{% endblock%}
```
**`output:`**
<img src ="regis1.JPG">

**`login.html`**

```html
{% extends 'profiles/header.html' %}

{% block content %}

	<form action="{% url 'profiles:login' %}" method="POST">
		<center><h1>LoginForm</h1></center>
		{% csrf_token %}
		{{form.as_p}}
		<input type="submit" value="login" name="submit">
	</form>

{% endblock %}
```
**`output:`**
<img src ="login1.JPG">

**`logout.html`**

```html
{% extends "profiles/header.html " %}

{% block content %}
	<h2>Logout success</h2>
{% endblock %}
```
**`output:`**
<img src ="logout.JPG">
