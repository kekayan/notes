---
toc: true
layout: post
description: Deploy Django App to Heroku Quick Draft while deploying
categories: [django,python]
title: How to deploy a Django Project to Heroku 
---

# Deploy a Django Project to Heroku :rocket:

## Prereqs :white_check_mark:

I ’ll assume that you have the familarity with the `Django` , `git` and have a project ready to deploy with it's `requirements.txt`.
And read my article on [Inital setting up](https://kekayan.github.io/notes/django/python/2020/05/19/djang-getting-started.html)
Meanwhile :thought_balloon: wondering how to deploy so you can demo it to someone or just deploy to production.

## Heroku :cloud:
Heroku is a cloud :cloud: application platform, a Platform-as-a-Service (PaaS). 
I feel Heroku is a great option to deploy our side projects and demos as a hobby dev. Also it has PostgreSQL Relational Database to try out free.

### Create an Account :checkered_flag:
If you don't already have an account Please Sign up [here](https://signup.heroku.com/) for an account.
It's free and simple.

### Install the Heroku CLI :checkered_flag:
```shell
curl https://cli-assets.heroku.com/install.sh | sh
```
if you want to know more on how to install CLI please check [here](https://devcenter.heroku.com/articles/heroku-cli#download-and-install)

## Shaping up the Project for Heroku deploy
Heroku needs the information about our app to run.So 
We have to do few changes and add some thing to our project.

### Gunicorn :gift:

Gunicorn 'Green Unicorn' is a Python WSGI HTTP Server for UNIX. 
It helps the Heroku to deploy our application across various “workers.”

so in our `requirements.txt`
we have to add `gunicorn`.
and if you want to locally install you can 
```shell
pip install gunicorn
```
also if you already don't have a `requirements.txt` .You can create one by 
```shell
pip freeze > requirements.txt
```
in your virtual environment.

### Procfile :checkered_flag:
Procfile is unique for heroku. We have to add it in our project’s root directory.It basically tells Heroku how our app should start and run.

You can manually create a procfile or run 

```shell
echo 'web: gunicorn config.wsgi --log-file -' > Procfile
```
Here `config` is my app which has the `wsgi.py` file.If you read my article [here](https://kekayan.github.io/notes/django/python/2020/05/19/djang-getting-started.html).You will know why i name my app as config.

So here we are telling heroku it's a web app with gunicorn server and the starting point is `wsgi.py` from `config` dir or app.

So you will need to replace it with `your_project_name.wsgi`


### Django-heroku :gift:
Heroku has created a python module called django-heroku that helps with settings, testing, and logging automatically.

same as Gunicorn it also has be to added to our 
`requirements.txt` as `django-heroku`.

we have add these two lines to our `settings.py`.
first import it at the top

```python
import django_heroku
```
and in the bottom call the method.

```python
django_heroku.settings(locals())
```
###  STATIC_ROOT :checkered_flag:

So again in `settings.py` let's add some changes belwo the variable called `STATIC_URL`. Let's add the `STATIC_ROOT` 
```python
STATIC_URL = '/static/'
STATIC_ROOT = os.path.join(BASE_DIR, 'static')
```

and some people create project directory as `PROJECT_DIR` variable and use it instead of `BASE_DIR`.So it will be

```python
PROJECT_DIR = os.path.dirname(os.path.abspath(__file__))
STATIC_URL = '/static/'
STATIC_ROOT = os.path.join(PROJECT_ROOT, 'static')
```

### To serve static files we need whitenoise :gift:
We need to add `whitenoise` to our `requirements.txt` file as well.
To install in environment
```
pip install whitenoise
```
Now edit the `settings.py` file and add `WhiteNoise` to the `MIDDLEWARE` list as following. 

```python
MIDDLEWARE = [
  'django.middleware.security.SecurityMiddleware',
  'whitenoise.middleware.WhiteNoiseMiddleware',
  # ...
]
```
The WhiteNoise middleware should be placed directly after the Django SecurityMiddleware and before all other middleware.

now add it below `STATIC_ROOT` in the the `settings.py` .
```python
STATICFILES_STORAGE = 'whitenoise.storage.CompressedManifestStaticFilesStorage'
```

### Database settings using dj-database-url :gift:

Add the following to the bottom of `settings.py`:
```python
import dj_database_url
DATABASES['default'] = dj_database_url.config(conn_max_age=600, ssl_require=True)
```
here you can find more on [my article](https://kekayan.github.io/notes/django/python/2020/05/19/djang-getting-started.html) on that and also heroku doc[doc](https://devcenter.heroku.com/articles/heroku-postgresql#connecting-with-django)
and add `psycopg2-binary` and `dj-database-url` to your `requirements.txt` file as well.

### runtime.txt :checkered_flag:
We have to create a file `runtime.txt` in the project root to specify the correct Python version .
If you use cli it will be 
```shell
echo 'python-3.8.2' > runtime.txt
```
or you can create the file using ide and mention your python version.


## Deployment :rocket:

First `git` commit the changes .
### Login to heroku :checkered_flag:
Inside out project root
```shell
heroku login
```
It will ask to click a link to login using web browser .

### create a Heroku App :checkered_flag:
```shell
heroku create demo-app
```

### Add a PostgreSQL database to your app :gem:

```shell
heroku addons:create heroku-postgresql:hobby-dev
```
You have to go your  [Heroku Dashboard](https://id.heroku.com/login) and access your recently created app (demo-app).

Click on the Settings menu and then on the button Reveal Config Vars and your `SECRET_KEY` below the database url and `DEBUG`  to false if it's not default false.

### deploy :rocket:

#### git push the app 
now push the commited git to heroku master

```shell
git push heroku master
```
#### maigrate  database
```shell 
heroku run maigrate
```
You have deployed your app now :tada: :tada: :tada:
## Quick Note :warning:

**I did this post while i was deploying incase if you find any mistake or stuck please let me know on twitter or  .** :envelope:  [email](mailto:notkekayan@gmail.com)