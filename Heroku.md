# How to Deploy a Django App on Heroku


## Requirements
```sh
pip freeze > requirements.txt
```

## Procfile
```sh
echo "web: gunicorn PROJECT_NAME.wsgi" > Procfile
```

## Dependencies
- gunicorn (an application server)
- whitenoise (for serving static files)
- psycopg2-binary (for postgres database)

Install them 

```sh
pip3 install gunicorn whitenoise psycopg2-binary
```

```sh
pip freeze > requirements.txt
```

## Runtime
Now you have to create a runtime.txt file on your root directory (just like the Procfile) and add the python version you’re using. To check which python version you're using, run:

```sh
python3 --version
```
Output:
```sh
Python 3.9.7
```

Now:
```sh
echo "python-3.9.7" > runtime.txt
```

## Settings file

```python
INSTALLED_APPS = [
    'whitenoise.runserver_nostatic',
    # ...
]

ALLOWED_HOSTS = ['127.0.0.1', '.herokuapp.com']

DEBUG = False

MIDDLEWARE = [
   # This is the default Django Security Middleware
   'django.middleware.security.SecurityMiddleware',
   
   # Add whitenoise middleware here
   'whitenoise.middleware.WhiteNoiseMiddleware',
   # ...
]


STATIC_ROOT = os.path.join(BASE_DIR, 'static')

STATICFILES_STORAGE = 'whitenoise.storage.CompressedManifestStaticFilesStorage'


DATABASES = {
    "default": {
        "ENGINE": "django.db.backends." + os.environ.get("DBENGINE", "sqlite3"),
        "NAME": os.environ.get("DBNAME", BASE_DIR / "db.sqlite3"),
        "USER": os.environ.get("DBUSER", "root"),
        "PASSWORD": os.environ.get("DBPASSWORD", ""),
        "HOST": os.environ.get("DBHOST", "127.0.0.1"),
        "PORT": os.environ.get("DBPORT", "3306"),
        "OPTIONS": json.loads(os.getenv("DATABASE_OPTIONS", "{}")),
    }
}
```

## Heroku 
### Login
```sh
heroku login
```

### Create project
```sh
heroku create PROJECT_NAME
```

### Commit and push to heroku
```sh
git add -A
git commit -am "commit message"
git push heroku main
```

### configure the database
To add the Postgres add-on, you have to run
```sh
heroku addons:create heroku-postgresql:hobby-dev --app PROJECT_NAME
```

Check postgres credentials
```sh
heroku pg:credentials:url
```


### Environment Variables
```sh
heroku config:add DEBUG=true
heroku config:add SECRET_KEY=jhskfjgsdjgldshkg
heroku config:add DBENGINE=postgresql_psycopg2
heroku config:add DBNAME=dkdfhakjhfkjdafh
heroku config:add DBHOST=ec2-54-172-175-251.compute-1.amazonaws.com
heroku config:add DBPASSWORD=hadgfaskfkjas
heroku config:add DBPORT=5432
heroku config:add DBUSER=jhagfashfkasg
heroku config:add DJANGO_ALLOWED_HOSTS=PROJECT_NAME.herokuapp.com,127.0.0.1,localhost,*
```

### Migrations and Migrate
```sh
heroku run python manage.py makemigrations
heroku run python manage.py migrate
heroku run python manage.py createsuperuser
```







