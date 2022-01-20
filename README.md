## Django lessons 1 and 2 of 5 - Setting up the app and creating models.




# How to Set Up a Django Application & Virtual Environment

## Python 3 Installation

1. In your terminal, check the version of Python running on your machine:

  ```
  python -V
  ```

  If you are **_not_** on a version of Python that is 3 or greater, then you'd have to install Python3 on your machine. Run the following command from terminal:
  ```
  brew install python3
  ```

## Setting Up a New Django Application - tunr_django

We'll be using Django as our backend framework for the next few lectures of Unit 4. Let's set up our Django project in advance of our Django lectures. In this tutorial, we will create and install Django on a new project called `tunr_django`. Follow the steps below.

1. Navigate to your sandbox folder and make a `tunr_django` directory inside your sandbox folder. Navigate into your folder.

  ```
  mkdir tunr_django
  cd tunr_django
  ```

2. Next, we're going to build a virtual environment. Virtual environments allow us to have multiple versions of Python on the same system and manage project dependencies and to use a specific version of Python for different projects. To manage our dependencies and virtual environments, we're going to use a tool called [pipenv](https://pipenv.readthedocs.io/) so make sure you have it installed with `pipenv --version`.

  If you don't have pipenv installed, easy! Homebrew to the rescue:

  ```
  brew install pipenv
```

3. Let's "activate" our virtual environment to ensure every subsequent command will use the virtual environment we created. Run the following command from the project root:

  ```
  pipenv shell
  ```

4. Open up the project in VS Code and take a look at the `Pipfile`. It should look similar to this:

  ```
  [[source]]
  name = "pypi"
  url = "https://pypi.org/simple"
  verify_ssl = true

  [dev-packages]

  [packages]
  django = "*"
  psycopg2-binary = "*"

  [requires]
  python_version = "3.7"
  ```

  Make sure `python_version` is set to a version that is 3 or greater. If the version mentioned in the `Pipfile` is already 3 or greater, then you can skip to the next step (i.e., install django).
  
  If it's set to an older version of Python (e.g., `2.7`), then you'd need to change it to the version of Python 3 that's available on your machine. You can check the version number by running `python3 -V` from your terminal. For example, if the version is `3.7.6`, then you'd have to update the `python_version` line in the `Pipfile` to `python_version="3.7.6"`.

5. Install django inside your `/tunr_django` folder:

  ```
  pipenv install django
  ```

  Running the above command will install Django and create the virtual environment where your dependencies for this project will be managed. Pipenv works a lot like npm does: it'll install our dependencies and track them in a `Pipfile`. This is similar to how `npm` works, the main difference being that pipenv does all the work for us by putting the dependencies in a separate location, so we don't have to worry about adding things to `.gitignore`.

6. Next, we're going to install the library for connecting Django to PostgreSQL:

  ```
  pipenv install psycopg2-binary
  ```

7. All we've done so far is install our dependencies and create our virtual environment. Now, we want to start our Django project:

  ```
  pipenv run django-admin startproject tunr_django .
  ```

  > Make sure you put the . on the end! This creates the project in the current directory instead of creating a new subfolder.

  Let's break down this command, because there are a few parts to it:

  - `django-admin` is the command line interface for interacting with Django. It
    has a few commands, of which `startproject` is the one to start a new Django
    project.
  - `tunr_django` is the name of our project. We add `.` after it so that the
    project is created in the current directory (the default is to create a new
    Django project in a new director).
  - `pipenv run` is required because we want to use the version of Django that we
    just installed using pipenv. If we leave off this part of the command, we'll
    use the version of the Django CLI that is installed globally (if there is
    one).

8. Let's also create our app (make sure you're running the following command within the virtual environment you created in step 3):

  ```
  $ django-admin startapp tunr
  ```

  Note: if django-admin doesn't work, you can replace it with `python3 manage.py`, assuming `manage.py` is in your current directory. Again, make sure you're running the following command within the virtual environment you created in step 3.

  This step creates an "app" inside of our project repo called `tunr`. `tunr_django` is the base django project, where we handle our routes. `tunr` is where we write our models, controllers, and templates.

  We can have many "apps" inside of a django project. This allows us to modularize our code, giving us flexibility and separation of concerns and making our code self-contained.

9. We need to include the app we generated. In `tunr_django/settings.py` find the `INSTALLED_APPS` constant dictionary. On the bottom line of the `INSTALLED_APPS` list, add `tunr`. Whenever you create a new app, you have to include it in the project.

  ```
  INSTALLED_APPS = [
      'django.contrib.admin',
      'django.contrib.auth',
      'django.contrib.contenttypes',
      'django.contrib.sessions',
      'django.contrib.messages',
      'django.contrib.staticfiles',
      'tunr'
  ]
  ```

10. Next, we need to create our database. By default, Django uses sqlite for its database. We'll use postgres instead, because it's more robust and better for web applications. 

- To ensure Postgres is running on your computer, **on macOS** type:

```bash
brew services list
```

- To ensure Posgres is running on your computer, **on Linux** type:

```bash
service postgresql status
```

(_macOS_) Postgres will only work if you see a service with a `Name` of postgresql and `Status` say `started` in green. If it is yellow, try running `brew services restart postgresql`. 

(_Linux_) Postgres will only work if you see a green circle and the words
"active (running)" somewhere in the output.

After running postgres, create a new file called `settings.sql` in the project root directory:

  ```
  touch settings.sql
  ```

  Inside `settings.sql`, add the following:

  ```
  -- settings.sql
  CREATE DATABASE tunr;
  CREATE USER tunruser WITH PASSWORD 'tunr';
  GRANT ALL PRIVILEGES ON DATABASE tunr TO tunruser;
  ```

  Then run the following command from the root directory:

  ```
  $ psql -U postgres -f settings.sql
  ```
  
 If you are getting a fatal error, try removing the '-U postgres' from the command 

11. Next, we need to connect our app to the database. In `tunr_django/settings.py`, find the `DATABASES` constant dictionary. Let's edit it to look like this:

  ```
  DATABASES = {
      'default': {
          'ENGINE': 'django.db.backends.postgresql',
          'NAME': 'tunr',
          'USER': 'tunruser',
          'PASSWORD': 'tunr',
          'HOST': 'localhost'
      }
  }
  ```

12. Now, in the terminal start the Django server by running

  ```
  python3 manage.py runserver
  ```

  `manage.py` contains a lot of management commands for Django. We'll see more later, but [here](https://docs.djangoproject.com/en/2.1/ref/django-admin/) is the full documentation if you are interested in what's going on behind the scenes. You can see a list of commands that `manage.py` offers by typing:

  ```
  python3 manage.py
  ```


13. Finally, navigate to `localhost:8000`. You should see a page welcoming you to Django!


## Next, lets make some models for our data  

## Models 

Let's start working with some data. In Django, we will write out models. Models
represent the data layer of our application. We store that data in our database.

First, lets create a Python class that inherits from the Django built in
`models.Model` class. Let's also define the fields within it. We do so like
this:

```python
# tunr/models.py
class Artist(models.Model):
    name = models.CharField(max_length=100)
    nationality = models.CharField(max_length=100)
    photo_url = models.TextField()
```

Here, we are defining three fields (which will be represented as columns in our
database): `name`, `photo_url` and `nationality`. `name` and `nationality` are
character fields which means that we must add an upper limit to how many
characters are in that database field. The `photo_url` will have unlimited
length. The full listing of the available fields are
[here](https://docs.djangoproject.com/en/2.1/ref/models/fields/).

Let's also add the magic method `__str__`. This method defines what an instance
of the model will show up as by default. It will be really helpful for debugging
in the future!

```python
class Artist(models.Model):
    name = models.CharField(max_length=100)
    nationality = models.CharField(max_length=100)
    photo_url = models.TextField()

    def __str__(self):
        return self.name
```

This is a brief example of how to write models in Django. The
[documentation](https://docs.djangoproject.com/en/2.1/topics/db/models/) is
fantastic and there are a number of
[built in field types](https://docs.djangoproject.com/en/2.1/ref/models/fields/#model-field-types)
that you can use for making very detailed models.

## Migrations

In the SQL class, we talked about how schema is enforced on the database side
when we use SQL databases. But here we are writing our schema on the Python
side! We have to translate that code into the schema for our database. We will
do so using migrations. In some frameworks, you have to write your migrations
yourself, but in Django the framework writes them for us!

In order to migrate this model to the database, we will run two commands. The
first is:

Windows users, you may need to add 'python -m' before some of your pipenv commands


```bash
$ python3 manage.py makemigrations
```

This will generate a migration file that gets all of its data from the code in
the `models.py` file. Go ahead and open it up - it should be in
`tunr/migrations/0001_initial.py`.

Every time you make changes to your models, run `makemigrations` again.

You should **NEVER** edit the migration files manually, as Django automatically takes care
of generating these migration files based on our model changes. Instead, edit the models
files and let django figure out what to generate from them by running
`makemigrations` again.

You **should** commit the migration files into git, however. They are crucial
for other people who want to run their own app.

When you've made all the changes you think you need, go ahead and run:\


```bash
$ python3 manage.py migrate
```

This will commit the migration to the database.

If you open up `psql`:
```
$ psql
```

and connect to the `tunr` database:
```
\c tunr
```
you'll see all the tables have now been created!

This is quite different than mongoDB, where the databases and collections get
created automatically as soon as you insert data into them.

<details>
<summary>What is a migration?</summary>
A set of changes/modifications intended for a database. They can be anything that makes a permanent change - creating columns, creating tables, changing properties, etc.
</details>



### Foreign Keys 

Let's also start filling out the Song model. We will define the class and then
add a foreign key. We do so like this:

```python
class Song(models.Model):
    artist = models.ForeignKey(Artist, on_delete=models.CASCADE, related_name='songs')
```

A foreign key is a field or column in one table that uniquely identifies a row
of another table. In this case, `Song` will contain a column called `artist`
that contains the ID of the associated artist. We don't have to define `id` in
the model, django and psql add them for us.

The `related_name` refers to how the model will be referred to in relation to
its parent -- you will see this in use later on. `on_delete` specifies how we
want the models to act when their parent is deleted. By using cascade, related
children will be deleted.

<details>
    <summary>What kind of relationship is implied by giving the Song table the foreign key from the artist table?</summary>

    1 -> Many
    Artist -> Song
    An artist can have many songs

</details>

What needs to happen now that we made a change to the model file?

```bash
python3 manage.py makemigrations
```

Check out the migrations folder. You should see something like `0002_song.py`.

Python automatically sequences the migration files and tries to give a
description for them - in this case, we added a song model, so it gives it the
name `song`.

Now run:

```
python3 manage.py migrate
```

And notice that it's all updated!

You can read more about migrations
[in the Migrations section of the documentation](https://docs.djangoproject.com/en/2.1/topics/migrations/)

If you want to see which migrations have been run already, use the command
`python3 manage.py showmigrations`.



### Admin Console 

Before we get too far, let's also create a superuser for our app. Django has
authentication (and authorization) right out of the box, so you don't have to
write it yourself or add a plugin.

In the terminal, run:

```bash
$ python3 manage.py createsuperuser
```

Then fill in the information in the boxes that pop up!

So far in this class, we have used seed files to add initial data to our
databases. We can also do that in Django
([see this article](https://docs.djangoproject.com/en/2.1/howto/initial-data/)),
but let's try something a little bit different instead.

Django has an admin dashboard built in, which gives us full CRUD functionality
straight out of the box.

Let's set it up! In `tunr/admin.py`, add the following code:

```python
from django.contrib import admin
from .models import Artist

admin.site.register(Artist)
```

**Now! Bear Witness To the Awesomeness of Django!!!**

Run your server again, then navigate to `localhost:8000/admin`. You can login
and get a full admin view where you have CRUD functionality for your model!

Create two Artists here using the interface.

## Finish the Song model 

- Add `title`, `album` and `preview_url` fields, then create and run the
  migrations.
- Register your Song model like you did with Artist.
- Finally create three songs using the admin site.

<details>
<summary>Solution: Modify Song Model</summary>

```python
class Song(models.Model):
    artist = models.ForeignKey(Artist, on_delete=models.CASCADE, related_name='songs')
    title = models.CharField(max_length=100, default='no song title')
    album = models.CharField(max_length=100, default='no album title')
    preview_url = models.CharField(max_length=200, null=True)

    def __str__(self):
        return self.title
```

</details>

<details>
<summary>Solution: Modify admin.py</summary>

```python
from django.contrib import admin
from .models import Artist, Song
admin.site.register(Artist)
admin.site.register(Song)
```

</details>

<details>
<summary>Solution: create migration</summary>

```bash
python3 manage.py makemigrations
```

</details>

<details>
<summary>Solution: run migration</summary>

```bash
python3 manage.py migrate
```

</details>


