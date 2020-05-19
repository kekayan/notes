---
toc: true
layout: post
description: My Django Customization when i start a project
categories: [django,python]
title: Django Personal Customization
---

# Django 


## Restructure

Let's start a new project by executing `django-admin`'s `startproject` command .I will name the project as config.
So i will execute following command in my python virtual environment.
```shell django-admin startproject config``` .

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

first thing i am going to do is restructure the dir by taking out inner `config` and `manage.py` to root.


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

## Settings.py 

I am going to install three more python packages 
```shell
pip install python-decouple
pip install dj-database-url
```

### python-decouple
Web apps have different environments params.For example
in Django database url, password, secret key, debug status, email host, allowed hosts. Best thing to do is decouple them from actual application.
Python Decouple is doing exactly that by separating the settings parameters from the source code.

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

Now in `.env` we can add our environment variables. For example our `.env` file looked like this.
```
DEBUG=True
SECRET_KEY=4drn&9w92v8=vrz2de2)0
SERVER=*.herokuapp.com
DATABASE_URL=sqlite:///db.sqlite3
```
> DON'T COMMIT YOUR `.env` FILE TO PUBLIC GITHUB repo.

Now in `settings.py` we can import the decouple and set the params to be taken from our `.env` file.

```python
from decouple import config
import dj_database_url
```
We can add an extra argument to the `config` function, to define a default value  as below incase if it couldn't be found in `.env`.
Also another argument called `cast` for the type. It can transform our String to mentioned type
```python
SECRET_KEY = config('SECRET_KEY',default='S#perS3crEt_1122KKKK')

DEBUG = config('DEBUG', default=False,cast=bool)

ALLOWED_HOSTS = ['localhost', '127.0.0.1',
                 config('SERVER', default='127.0.0.1')]

DATABASES = {
    'default': dj_database_url.config(
        default=config('DATABASE_URL')
    )
}

```

### dj-database-url

Above you have seen already we used [dj-database-url ](https://github.com/jacobian/dj-database-url)while setting database environment variable.
The `dj_database_url.config` method returns a Django database connection dictionary, populated with all the data specified in your URL. There is also a conn_max_age argument to easily enable Django’s connection pool.


## Django Secret Key

The easiest way to generate a secret key is using django it self.Django generates a secret key every time when we  create a new project. We can use that function as below

```python
from django.core.management.utils import get_random_secret_key
print(get_random_secret_key())
```


one liner 
```shell
python -c 'from django.core.management.utils import get_random_secret_key; print(get_random_secret_key())'
```

## Next 

I will write on static files serving for heroku and other things when i get time