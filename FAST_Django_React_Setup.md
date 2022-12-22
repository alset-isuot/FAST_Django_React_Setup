# FAST FULL-STACK: Front-End (React) and Back-End (Django) Setup and Connection

# Create empty root directory --  we're in root directory for application
```bash
mkdir Day1
```
```bash
cd Day1
```
```bash
mkdir Django_Auth_Review
```
```bash
cd Django_Auth_Review 
```    
## open two terminals to empty root directory -- 'Django_Auth_Review'
name each one, and select color of each, one for back-end (server) and one for front-end (client)

# Use Frontend terminal
```bash
npm create vite 
```
Type in name of the frontend

? Project name:
> client

We scaffolding our frontend by selecting template presets of:

? Select a framework: 

> react

? Select a variant:

>react or javascript

```bash
cd client
```
```bash
npm install
```
## How to fix Vite Image: Move public/vite.svg image into src/assets folder, mimic what is done with reactLogo in App.jsx
## "sanity" check, check that front end is working correctly, Ctrl+c to end
```bash
npm run dev
```

## install Frontend dependencies -- note: you will still need to use import on files using these
```bash
npm install --save axios react-router-dom bootstrap react-bootstrap       
```

# Use BACK-END terminal
Create virtual environment, use '.venv' instead of 'venv', helps the vscode debugger to recognize the virtual environment
```bash
python3 -m venv .venv
```
Activate virtual environment
```bash
source .venv/bin/activate
```
Create .gitignore file
```bash
touch .gitignore 
```
Add .venv to .gitignore file
```bash
code .gitignore
```
Django pip installs, backend setups
```bash
pip install django psycopg2 djangorestframework markdown django-filter
```
```bash
python -m django startproject server
```
```bash
cd server
```
```bash
python -m django startapp server_app
```
Create static folder
```bash
mkdir static
```
Create .gitignore file
```bash
touch .gitignore 
```
add static to .gitignore, when someone else clones our app, they will use build command to repopulate the 'static' folder
```bash
code .gitignore
```
Create urls.py for django app
```bash
touch server_app/urls.py
```
If wanted name of database is already been used, you must drop it to create a new one with same name
```bash
dropdb database_name
```
Create database
```bash
createdb database_name
```
Save requirements
```bash
pip freeze > requirements.txt
```

## "sanity" check, check that back end is working correctly, Ctrl+c to end
```bash
python3 manage.py runserver
```

# Connect BACK-END and FRONT-END
## Use BACK-END terminal -- Edit back-end files
### Edit server/settings.py, add code to file
```bash
code server/settings.py
```

```python
INSTALLED_APPS = [
  #...
  'rest_framework',
  'server_app', #what ever you named the app part of django backend with the startapp command
]

DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.postgresql',
        'NAME': 'database_name', #name you used with createdb command
    }
}

STATIC_URL = 'static/' 
#set static directory path in settings.py
STATICFILES_DIRS = [ BASE_DIR / 'static'  ]   #this is where out static stuff lives

AUTH_USER_MODEL = 'server_app.AppUser' 
```

### Edit server/urls.py
```bash
code server/urls.py
```
```python
from django.contrib import admin
from django.urls import path, include

urlpatterns = [
    path('admin/', admin.site.urls),
    path('', include('server_app.urls')),
]
```

### Edit server_app/urls.py
```bash
code server_app/urls.py
```

```python
from django.urls import path
from . import views
urlpatterns = [
    path('', views.index),
]
```

### Edit server_app/views.py
```bash
code server_app/views.py
```
```python
from django.shortcuts import render
from django.http import HttpResponse, JsonResponse
from rest_framework.decorators import api_view
# create your views here
def index(request):
    """
    homepage
    """
    print('index')
    homepage = open('./static/index.html').read()
    return HttpResponse(homepage)

# API ROUTES
```

### Edit server_app/models.py
```bash
code server_app/models.py
```
```python
from django.db import models
from django.contrib.auth.models import (AbstractUser)

# Inheriting from 'AbstractUser' lets us use all the fields of the default User, 
# and overwrites the fields we need to change
# This is different from 'AbstractBaseUser', which only gets the password
# management feactures from the default User, and needs the developer to 
# define other relevant fields.

# Create your models here.
class AppUser(AbstractUser): #userCLassName
    """
    user account
    """
    email = models.EmailField(
        max_length=255,
        unique=True,
    )

# notice the absence of a "Password field", because its already built in
# Django uses the 'username' to identify users by default, but many modern applications use 'email' instead
    #we're using email as username
    USERNAME_FIELD = 'email' # email is the data that is authenticating user, this allows us to just send in the email
    REQUIRED_FIELDS = [] # Email and Password are required by default
```

### Run Migrations
```bash
python3 manage.py makemigrations    # Packages up your model changes into individual migration files 
python3 manage.py migrate     # Applies migration files to your database
```

## Use FRONT-END terminal -- Edit front-end files
### Edit vite.config.js
```bash
code vite.config.js
```

```js
import { defineConfig } from 'vite'
import react from '@vitejs/plugin-react'
// https://vitejs.dev/config/
export default defineConfig({
  base: "/static/",
  build: {
    outDir: "../server/static",
    emptyOutDir: true, // makes sure old build is deleted before a new one is run
    sourcemap: true, // helps with chrome devtools
  },
  plugins: [react()]
})
```

### Edit package.json
```bash
code package.json
```

```js
  "scripts": {
    "dev": "vite",
    "build": "vite build",
    "build:watch": "vite build --watch",
    "preview": "vite preview"
  },
```

```bash
npm run build          # will build the project once
npm run build:watch   # will build the project every time a front end file is saved
```

# See if Django is hosting correctly
## use Backend terminal
```bash
python3 manage.py runserver
```
