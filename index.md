
This project implements a Web application that displays grocery store scores that are generated from multiple ranking parameters. The scorecard (at least at this point) will focus on local grocery stores and earned points will be based on affirmative conditions (i.e., verifiable products or services) rather than the absence of such conditions.

### Public Facing Interface

* A home page that displays the grocery stores in order of aggregate ranking (i.e., highest to lowest)
* Detailed pages with rankings across categories (see below), optional comments, and date last updated that can be accessed by clicking on the grocery store names.
* A search field (TBD)
* A feedback link (form or email)
* A link to the Admin Interface

### Private Admin Interface

* A login form for approved users.
* Add/Update forms that will be used to submit a new ranking or update an existing one.
* Ability to remove a store from the list.
* Management of user accounts.

### Example Ranking Factors*

| Category | Sub-Categories |
| -------- | ------- |
| Bulk Shopping  | Vegetables/Fruits, Breads, Grains, Beans, Pasta, Nuts, Flour/Sugar, Spices, Coffee/Tea, Oils/Vinegar, Nut Butters, Cleaning Liquids/Powders, Beauty Products|
| Quality Offerings | Organic, Sustainable, Fair Trade, Non-GMO, Cage Free, Pasture Raised, Vegan Substitutes, Freshness of Produce/Perishables, Plastic-Free Options, Eco-Friendly Beauty/Cleaning Products |
| Shopping Experience | Friendliness, Checkout Lines/Ease of Checkout, Helpfulness, Knowledgeable, Wide Aisles, Well Stocked, Variety, Price, Store Cleanliness |
| Store Extras | Mission, Fresh Bakery, Compost, Recycling Options, Water Refill, Plants/Flowers, Response to Feedback/Emails, Encourage BYOB (signs/credits), Offer Bags/Containers, Paperless Receipts |

* To be integrate as shareable and/or editable document


# Implementation (In Progress)

## Web Framework

As mentioned, the Web Framework used will be [Django](https://www.djangoproject.com) which is implemented with Python. The application will be running on a Mac OS (Python v3.7.6 and Django v3.0) during initial testing and then moved to the Amazon AWS platform.

Since the setup, at least initially, is not geared to a heavy-weight and/or production environment I will skip the installation of Apache and mod_wsgi as suggested in the [installation](https://docs.djangoproject.com/en/3.0/topics/install/) instructions.

Similarly, I will use [_SQLite_](https://www.sqlite.org/index.html) as the database which already comes bundled with Django and should be adequately support our schema design and processing demands. Since SQLite does not effectively deal with concurrency, switching to a more powerfull database such as MySQL, MariaDB or PostgreSQL is recommended if application is made externally facing.

### Installing Django

After installing _pip_ you can run the following command (output shown below):
```
python3 -m pip install django
Collecting django
  Downloading Django-3.0.7-py3-none-any.whl (7.5 MB)
     |████████████████████████████████| 7.5 MB 4.3 MB/s 
Collecting pytz
  Downloading pytz-2020.1-py2.py3-none-any.whl (510 kB)
     |████████████████████████████████| 510 kB 3.7 MB/s 
Collecting asgiref~=3.2
  Downloading asgiref-3.2.7-py2.py3-none-any.whl (19 kB)
Collecting sqlparse>=0.2.2
  Downloading sqlparse-0.3.1-py2.py3-none-any.whl (40 kB)
     |████████████████████████████████| 40 kB 4.3 MB/s 
Installing collected packages: pytz, asgiref, sqlparse, django
Successfully installed asgiref-3.2.7 django-3.0.7 pytz-2020.1 sqlparse-0.3.1
```

After the installation we can always confirm the version by running:
```
python3 -m django --version
3.0.7
```

### Creating the Initial Project and App Structure

Using the _django-admin_ utility allows to easily create our initial project structure. From our root git directory run:
```
django-admin startproject sustainable .
```
In addition to the _manage,py_ (more on this later), the sub-directory _sustainable_, which is the actual Python package for the project, will be created. Using the handy _tree_ command (run _brew install tree_ to install) we can examine the generated files under this directory which we will configure as the application evolves:
```
tree sustainable/
sustainable/
├── __init__.py
├── asgi.py
├── settings.py
├── urls.py
└── wsgi.py
```

We can now test, from the git root directory, that the basic project works by running:

```
bash-3.2$ python3 manage.py migrate
Operations to perform:
  Apply all migrations: admin, auth, contenttypes, sessions
Running migrations:
  Applying contenttypes.0001_initial... OK
  Applying auth.0001_initial... OK
  Applying admin.0001_initial... OK
  Applying admin.0002_logentry_remove_auto_add... OK
  Applying admin.0003_logentry_add_action_flag_choices... OK
  Applying contenttypes.0002_remove_content_type_name... OK
  Applying auth.0002_alter_permission_name_max_length... OK
  Applying auth.0003_alter_user_email_max_length... OK
  Applying auth.0004_alter_user_username_opts... OK
  Applying auth.0005_alter_user_last_login_null... OK
  Applying auth.0006_require_contenttypes_0002... OK
  Applying auth.0007_alter_validators_add_error_messages... OK
  Applying auth.0008_alter_user_username_max_length... OK
  Applying auth.0009_alter_user_last_name_max_length... OK
  Applying auth.0010_alter_group_name_max_length... OK
  Applying auth.0011_update_proxy_permissions... OK
  Applying sessions.0001_initial... OK
```
and then:

```
bash-3.2$ python3 manage.py runserver
Watching for file changes with StatReloader
Performing system checks...

System check identified no issues (0 silenced).
April 25, 2023 - 20:36:14
Django version 3.0.7, using settings 'sustainable.settings'
Starting development server at http://127.0.0.1:8000/
Quit the server with CONTROL-C.
```

Going to that [url](http://127.0.0.1:8000/) you should see a standard Django welcome page (in my case "The install worked successfully! Congratulations!"). To use a port other than the default one, you can run the same command with the target port at the end.

Now we will run (from the root directory) the command to create the specific app which we will name _grocery_ and which will contain our query/display code:
```
python3 manage.py startapp scorecard
tree scorecard
scorecard
├── __init__.py
├── admin.py
├── apps.py
├── migrations
│   └── __init__.py
├── models.py
├── tests.py
└── views.py
```
Finally, before moving on to the models, lets test auto-generating a simple index file and verify that it works (like the standard Django tutorials suggest)

Create a _scorecard/urls.py_ file and add the below code:
```
from django.urls import path

from . import views

urlpatterns = [
    path('', views.index, name='index'),
]
```

In the _scorecard/views.py_ add the below content:
```
from django.http import HttpResponse


def index(request):
    return HttpResponse("This is my test of the scorecard index file.")
```

To integrate _scorecard_, in main _sustainable/urls.py_ add the below code:
```
from django.contrib import admin
from django.urls import include, path

urlpatterns = [
    path('scorecard/', include('scorecard.urls')),
    path('admin/', admin.site.urls),
]
```

Run the application as before (_python3 manage.py runserver_) and verify the new url rendering at http://127.0.0.1:8000/scorecard with the text you just added.
