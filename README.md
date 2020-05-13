# User Registration and User Authentication



# Creating a Django Project & Application
- After installing Django, you need to create a project using django
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
      
### Sync With database
Now that we’ve created the model, it’s time to add it to the database. To do this we need to open Command Promnt and run these two commands: 
-	python manage.py makemigrations 
-	python manage.py migrate

Your terminal output should look something like this:

<img src ="docmi.JPG">

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
We need to import Django forms first (from django import forms). Next, we have class Meta. Finally, we can say which field(s) should end up in our form. In this scenario if we want only few fields then metion them in a list formate.
