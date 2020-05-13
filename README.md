# User Registration and User Authentication

Django framework provide a built-in user management and authorization feature. You can use it to add user group and user account in Django project admin web page . You can also use the django.contrib.auth module to integrate the user authorization feature in your python source code. This article will tell you how to use it to implement a user registration and login example.


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

### View
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

### templates

**`header.html`**
```<!DOCTYPE html>
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
**`output:`**

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


**`logout.html`**

```html
{% extends "profiles/header.html " %}

{% block content %}
	<h2>Logout success</h2>
{% endblock %}
```
**`output:`**
