
This project implements a Web application that displays grocery store scores that are generated from multiple ranking parameters. The scorecard (at least at this point) will focus on local grocery stores and earned points will be based on affirmative conditions (i.e., verifiable products or services) rather than the absence of such conditions.

## Public Facing Interface

* A home page that displays the grocery stores in order of aggregate ranking (i.e., highest to lowest)
* Detailed pages with rankings across categories (see below) and comments that can be accessed by clicking on the grocery store names.
* A search field (TBD)
* A link to the Admin Interface

## Private Admin Interface

* A login form for approved users.
* An update form that will be used to submit a new ranking or update an existing one.
* Management of user accounts.

## Example Ranking Factors*

| Category | Sub-Categories |
| -------- | ------- |
| Bulk Shopping  | Vegetables/Fruits, Breads, Grains, Beans, Pasta, Nuts, Flour/Sugar, Spices, Coffee/Tea, Oils/Vinegar, Nut Butters, Cleaning Liquids/Powders, Beauty Products|
| Quality Offerings | Organic, Sustainable, Fair Trade, Non-GMO, Cage Free, Pasture Raised, Vegan Substitutes, Freshness of Produce/Perishables, Plastic-Free Options, Eco-Friendly Beauty/Cleaning Products |
| Shopping Experience | Friendliness, Checkout Lines/Ease of Checkout, Helpfulness, Knowledgeable, Wide Aisles, Well Stocked, Variety, Price, Store Cleanliness |
| Store Extras | Mission, Fresh Bakery, Compost, Recycling Options, Water Refill, Plants/Flowers, Response to Feedback/Emails, Encourage BYOB (signs/credits), Offer Bags/Containers, Paperless Receipts |

* Integrate as shareable and/or editable document


# Implementation (In Progress)

## Web Framework

As mentioned, the Web Framework used will be [Django](https://www.djangoproject.com) which is implemented with Python. The application will be running on a Mac OS (Python v3.7.6 and Django v3.0)

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
django-admin startproject scorecard .
```
In addition to the _manage,py_ (more on this later), the sub-directory _scorecard_, which is the actual Python package for the project, will be created. Using the handy _tree_ command (run _brew install tree_ to install) we can examine the generated files under this directory which we will configure as the application evolves:
```
tree scorecard/
scorecard/
├── __init__.py
├── asgi.py
├── settings.py
├── urls.py
└── wsgi.py
```

We can now test, from the git root directory, that the basic project works by running:
```
python3 manage.py runserver
```

If startup goes well you should get the below message:
```
June 08, 2020 - 21:03:44
Django version 3.0.7, using settings 'scorecard.settings'
Starting development server at http://127.0.0.1:8000/
Quit the server with CONTROL-C
```

Going to that [url](http://127.0.0.1:8000/) you should see a standard Django welcome page (in my case "The install worked successfully! Congratulations!"). To use a port other than the default one, you can run the same command with the target port at the end.

