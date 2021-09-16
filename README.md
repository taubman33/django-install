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

## Submission
Please open a Github issue on this repo indicating whether or not the installation was successful for you, along with a screenshot of the Django welcome page.
  
  **_If you are running into errors or issues, please file a Github issue on this repo and attach screenshots of all the errors you are getting so an instructor can assist you with setup next week prior to lecture._**
  
## Troubleshooting
Getting a `psql: FATAL: role “postgres” does not exist` error? Check out [this StackOverflow article](https://stackoverflow.com/questions/15301826/psql-fatal-role-postgres-does-not-exist).
  
  
***Submission Due 5/27 at 10am ET***
  
  
