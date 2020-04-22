# Django Interview

## Basics


**Q What is django? What do you like about it (Can be re-phrased as what are the features of django?)**

Ans - Django is a high-level Python web framework that encourages rapid development and clean, pragmatic design.

Features : 

- Admin Interface (CRUD)
- Templating
- Form handling
- Internationalization
- Session, user management, role-based permissions
- Object-relational mapping (ORM)
- Testing Framework
- Fantastic Documentation
- Open Source and huge collection of available packages


**Q. What is the architecture that django follows?**

Ans - Django follows the Model-Template-View (MTV) architecture. There is separation of concerns between the database interfacing classes (Model), request-processing classes (View), and a templating language for the final presentation (Template).

**Q. What is the latest version of Django and which one you are uisng?**

Ans - The latest version of django is, Django - 3.0. I am using Django 2.2 which is LTS (Long term support) release.


**Q. Is django an MVC framework?**

Ans - Django follows MVC pattern very closely but it uses slightly different terminology. Django is essentially an MTV (Model-Template-View) framework. Django uses the term Templates for Views and Views for Controller. In other words, in Django views are called templates and controllers are called views. Hence our HTML code will be in templates and Python code will be in views and models.


**Q. What popular websites use django? Can you name some?**

Ans Disqus, Instagram, Mozilla, NASA, The Washington Post etc.


**Q. Explain how you can set up the Database in Django?**

Ans - You can use the command edit mysite/setting.py , it is a normal python module with module level representing Django settings.

```python
DATABASES = {
    'default': {
        # 'ENGINE': 'django.db.backends.sqlite3',
        # 'NAME': os.path.join(BASE_DIR, 'db.sqlite3'),
        # 'OPTIONS': {
        #    'timeout': 20,
        # }
        'ENGINE': 'django.db.backends.postgresql',
        'NAME': 'name_of_database',
        'USER': 'postgres',
        'PASSWORD': 'password',
        'HOST': 'localhost',
        'PORT': 5432,
        
    }
}
```

Django uses SQLite by default; it is easy for Django users as such it won’t require any other type of installation. In the case your database choice is different that you have to the following keys in the DATABASE ‘default’ item to match your database connection settings

Engines: you can change database by using ‘django.db.backends.sqlite3’ , ‘django.db.backeneds.mysql’, ‘django.db.backends.postgresql_psycopg2’, ‘django.db.backends.oracle’ and so on
Name: The name of your database. In the case if you are using SQLite as your database, in that case database will be a file on your computer, Name should be a full absolute path, including file name of that file.
If you are not choosing SQLite as your database then setting like Password, Host, User, etc. must be added.

**Q. Mention what does the Django templates consists of?**

Ans - The template is a simple text file.  It can create any text-based format like XML, CSV, HTML, etc.  A template contains variables that get replaced with values when the template is evaluated and tags (% tag %) that controls the logic of the template.

**Q. Explain the use of session framework in Django?**

Ans - In Django, the session framework enables you to store and retrieve arbitrary data on a per-site-visitor basis.  It stores data on the server side and abstracts the receiving and sending of cookies.  Session can be implemented through a piece of middleware.

**Q. Explain the migration in Django and how you can do in SQL?**

Ans - Migration in Django is to make changes to your models like deleting a model, adding a field, etc. into your database schema.  There are several commands you use to interact with migrations.

Migrate
Makemigrations
Sqlmigrate
To do the migration in SQL, you have to print the SQL statement for resetting sequences for a given app name.

django-admin.py sqlsequencreset

Use this command to generate SQL that will fix cases where a sequence is out sync with its automatically incremented field data.


**Q. What is Unicode, what is UTF-8 and how do they relate?**

Ans - Unicode is an international encoding standard that works with different languages and scripts. It consists of letters, digits or symbols representing characters from across the world. UTF-8 is a type of encoding, a way of storing the code points of Unicode in a byte form, so you can send Unicode strings over the network or store them in files.”


**Q. List out the inheritance styles in Django?**

Ans - In Django, there is three possible inheritance styles

*Abstract base classes:* This style is used when you only wants parent’s class to hold information that you don’t want to type out for each child model.

*Multi-table Inheritance:* This style is used If you are sub-classing an existing model and need each model to have its own database table

*Proxy models:* You can use this model, If you only want to modify the Python level behavior of the model, without changing the model’s fields


## Intermediate

**Q. What are fixtures?**

Ans - it’s sometimes useful to pre-populate your database with hard-coded data when you’re first setting up an app. You can provide initial data with fixtures.

As an example, though, here’s what a fixture for a Person model might look like in JSON:
```json
[
  {
    "model": "myapp.person",
    "pk": 1,
    "fields": {
      "first_name": "John",
      "last_name": "Lennon"
    }
  },
  {
    "model": "myapp.person",
    "pk": 2,
    "fields": {
      "first_name": "Paul",
      "last_name": "McCartney"
    }
  }
]
```
You can load data by calling 
```python
manage.py loaddata <fixturename>
```


**Q. Middlewares - what they are and how they work?**

Ans - Middlewares are hooks to modify Django request or response object. It’s a light, low-level “plugin” system for globally altering Django’s input or output.

You can use middleware if you want to modify the request i.e HttpRequest object which is sent to the view. Or you might want to modify the HttpResponse object returned from the view.

Example - SecurityMiddleware, SessionMiddleware, AuthenticationMiddleware etc...

![Middleware](attachments/middleware.png)


**Q. what is signals and how to use it? What are two important parameter in signals?**

Ans - Signals allow certain senders to notify a set of receivers that some action has taken place. They’re especially useful when many pieces of code may be interested in the same events.

Some of the most used models’ signals are the following:

- *pre_save/post_save:* This signal is thrown before/after the method save().

- *pre_delete/post_delete:* Before after delete a model’s instance (method delete()) this signal is thrown.

- *pre_init/post_init:* This signal works before/after instantiating a model (__init__() method)

**Connecting Signals**

With the @receiver decorator, we can link a signal with a function:

```python
from django.db.models.signals import post_save
from django.dispatch import receiver

from someapp.models import MyModel

@receiver(post_save, sender=MyModel)
def my_function_post_save(sender, **kwargs):
   # do the action…
```

Every time that a MyModel’s instance ends to run its save() method, the my_function_post_save will start to work.





## Advanced

Q. How will you get list of logged-n users?

Ans - For this, you can query the Session model for non-expired sessions, then turn the session data into users.

```python
from django.contrib.auth.models import User
from django.contrib.sessions.models import Session
from django.utils import timezone

def get_all_logged_in_users():
    # Query all non-expired sessions
    # use timezone.now() instead of datetime.now() in latest versions of Django
    sessions = Session.objects.filter(expire_date__gte=timezone.now())
    uid_list = []

    # Build a list of user ids from that query
    for session in sessions:
        data = session.get_decoded()
        uid_list.append(data.get('_auth_user_id', None))

    # Query all logged in users based on id list
    return User.objects.filter(id__in=uid_list)
```


*Sources*
[list of logged-n users](https://stackoverflow.com/questions/2723052/how-to-get-the-list-of-the-authenticated-users)

[Understanding Django Signals](https://www.bedjango.com/blog/understanding-django-signals/)


