# FAST Frontend (React) and Backend (Django) Setup and Connection

# Create empty root directory --  we're in root directory for application
```bash
mkdir Day1
cd Day1
mkdir Django_Auth_Review
cd Django_Auth_Review 
```    
## open two terminals to empty root directory -- 'Django_Auth_Review'
name each one, and select color of each, one for backend(server) and one for frontend (client)

# Use Frontend terminal
```bash
npm create vite 
```
> Project name: 'client'
> Select a framework: React
> Select a variant: JavaScript
```bash
cd client
```
```bash
npm install
```
## move public/vite.svg image into src/assets folder, mimic what is done with reactLogo in App.jsx
## "sanity" check, check that front end is working correctly, Ctrl+c to end
```bash
npm run dev
```

## install Frontend dependencies -- # note: you will still need to use import on files using these
```bash
npm install --save axios react-router-dom bootstrap react-bootstrap       
```

# use Backend terminal
```bash
python3 -m venv venv
```
```bash
source venv/bin/activate
```
```bash
touch .gitignore 
```
add venv to file
```bash
code .gitignore
```
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
```bash
mkdir static
```
```bash
touch .gitignore 
```
add static to gitignore
```bash
code .gitignore
```
```bash
touch server_app/urls.py
```
```bash
dropdb database_name
```
```bash
createdb database_name
```
```bash
pip freeze > requirements.txt
```

## "sanity" check, check that back end is working correctly, Ctrl+c to end
```bash
python3 manage.py runserver
```

# Connect Backend and Frontend
## use backend terminal -- Edit backend files
### Edit server/settings.py
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
# Create your models here.
class AppUser(AbstractUser): #userCLassName
    """
    user account
    """
    email = models.EmailField(
        max_length=255,
        unique=True,
    )

    # A user account must be active to log in, ect.
    is_active = models.BooleanField(
        default=False, # might need to turn to True
        help_text='Designates whether this user should be treated as active. Unselect this instead of deleting accounts.', verbose_name='active'
    )

    USERNAME_FIELD = 'email'
    REQUIRED_FIELDS = []
```

### Run Migrations
```bash
python3 manage.py makemigrations
python3 manage.py migrate
```

## use Frontend terminal -- Edit frontend files
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
npm run build
npm run build:watch
```

# See if Django is hosting correctly
## use Backend terminal
```bash
python3 manage.py runserver
```
