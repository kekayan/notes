---
toc: true
layout: post
description: My Django Customization when i start a project
categories: [django,python]
title: Django Project and Settings Customizations
---

# Django 

Django is a high-level Python Web framework that encourages rapid development and clean, pragmatic design. Usually, when I start a Django project I do a few customizations. So today I wanted to note it down that so I can refer later and others will also find useful. And I also assume that you have familiar with Django and Anaconda or other virtual environments.


## Restructure

Let's start a new project by executing `django-admin`'s `startproject` command in the python virtual environment. 

```shell 
django-admin startproject config
``` 


Now the directory structure will be 

```
rootdir   
│
└───config
    │   manage.py
    │
    └───config
        │   __init__.py
        │   settings.py
        │   urls.py
        │   wsgi.py
        │   asgi.py
```

As a first step, I will restructure dir by taking out inner `config` and `manage.py` to the root dir.


```
rootdir
│  manage.py
└──config
    │ __init__.py
    │ settings.py
    │ urls.py
    │ wsgi.py
    │ asgi.py
```
or this can be done easily by executing 
```shell 
django-admin startproject config .
``` 
in the project dir itself.

## Settings.py 

Let's see the modifications I usually do in the `settings.py`. Because we need to decouple our settings from our environments. Also, there are many ways to achieve. I will start with the simplest and effective ones.
Let's install two more python packages 


```shell
pip install python-decouple
pip install dj-database-url
```

### why python-decouple
Web apps have different environments params. For example,
in Django database URL, password, secret key, debug status, email host, allowed hosts will take different values for dev environment to the production environment. One of the best practice is decoupling them from the actual application.
Python Decouple package is doing exactly that by separating the settings parameters from the source code.

First create a file named `.env` in the root of the project. 
```
rootdir
│  .env
│  manage.py
└──config
    │ __init__.py
    │ settings.py
    │ urls.py
    │ wsgi.py
    │ asgi.py
```

Now in `.env` file we can add our environment variables. For example our `.env` file will look like this.
```
DEBUG=True
SECRET_KEY=4drn&9w92v8=vrz2de2)0
SERVER=*
DATABASE_URL=sqlite:///db.sqlite3
```
> DON'T COMMIT YOUR `.env` FILE TO PUBLIC GITHUB repo.

Now in `settings.py` we can import the decouple and set the params to be taken from our `.env` file.

```python
from decouple import config
```
Now I can use the `config` function from decouple module to read the `.env` file and the params we set.
The `config` method will take extra arguments to define a default value as below incase if it couldn't find the parm in `.env`.
Also, another argument called `cast` for the type of params. It can transform our String params value to `cast` argument type

```python
SECRET_KEY = config('SECRET_KEY',default='S#perS3crEt_1122KKKK')

DEBUG = config('DEBUG', default=False,cast=bool)

ALLOWED_HOSTS = ['localhost', '127.0.0.1',
                 config('SERVER', default='127.0.0.1')]
```

### why dj-database-url


Like I already said we will have different databases for our dev to prod environments. So we need to decouple it as well. That’s where [dj-database-url ](https://github.com/jacobian/dj-database-url) coming to rescue. The `dj_database_url.config` method returns a Django database connection dictionary, populated with all the data specified in our URL. There is also a conn_max_age argument to easily enable Django’s connection pool. So now we only need to define our URL in the `.env` file.

```python
import dj_database_url
```

```python
DATABASES = {
    'default': dj_database_url.config(
        default=config('DATABASE_URL')
    )
}
```
now we can set urls in the `.env`
```
DEBUG=True
SECRET_KEY=4drn&9w92v8=vrz2de2)0
SERVER=*
DATABASE_URL=sqlite:///db.sqlite3
```

## Django Secret Key
Incase if you want to change the secret key.
The easiest way to generate a secret key is by using Django itself. Django generates a secret key every time when we create a new project. We can use that function as below

```python
from django.core.management.utils import get_random_secret_key
print(get_random_secret_key())
```

one liner 
```shell
python -c 'from django.core.management.utils import get_random_secret_key; print(get_random_secret_key())'
```

## Next 
I have added the STATIC file serving and Deployment to Heroku on [this note](https://kekayan.github.io/notes/django/python/2020/05/23/django-deploy-to-heroku.html)

**Meanwhile if you find any mistake please let me know  .** :envelope:  [email](mailto:notkekayan@gmail.com)