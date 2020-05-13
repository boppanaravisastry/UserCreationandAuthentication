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

