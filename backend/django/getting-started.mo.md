# [MO] Get Started with Django, in 3/4 hour (~45 min)

# Owner: [Tycho Tatitscheff](https://github.com/tychota)

# Prerequisites (~10 min)

- Have **HomeBrew** installed (`ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)`) (~3 min)
- Have `$PATH` environment variable priorizing Homebrew package (`export PATH=/usr/local/bin:/usr/local/sbin:$PATH` in .bashrc or similar) (~3 min)
- Have **Python3** installed (`brew install python3`)  (~2 min)
- Have **virtualenv** installed (`pip install virtualenv`) (~1 min)

# Steps (~35 min)

## Initialise a new python project (~5 min)

- Create a directory for the project: `mkdir ~/Code/django_formation`
- Go to the directory: `cd ~/Code/django_formation`
- Init a git directory: `git init`
- Create a python env for your project `virtualenv -p $(which python3) .venv`
- Add this python env to project `.gitignore`: `echo ".venv" > .gitignore`
- Activate the virtualenv `source .venv/bin/activate`

> **CHECK 1**: You should see the name of the env in front or your terminal prompt

> **CHECK 2**: Python should now points to your env:
> ```bash
> (.venv) ➜  django git:(master) ✗ which python
> ~/Code/django_formation/django/.venv/bin/python
> ```

> **CHECK 3**: you should have a `.env` and a `.git` folder, plus a `.gitignore` file
> ```bash
> (.venv) ➜  django git:(master) ✗ ls -lath
> drwxr-xr-x   9 user  staff   306B Aug  3 14:18 .git
> drwxr-xr-x   5 user  staff   170B Aug  3 14:18 .
> -rw-r--r--   1 user  staff     6B Aug  3 14:18 .gitignore
> drwxr-xr-x   7 user  staff   238B Aug  3 14:16 .venv
> drwxr-xr-x  67 user  staff   2.2K Aug  3 14:03 ..
> ```

## Install django (~1 min)

- Install django with pip:  `pip install django`
- Save the dependencies to a lockfile: `pip freeze > requirements.txt`

> **CHECK 1**: you should have a `requirements.txt` containing:
> ```bash
> (.venv) ➜  django git:(master) ✗ cat requirements.txt
> Django==1.11.4
> pytz==2017.2
> ```

> **CHECK 2**: django commands should be available `django-admin.py`

## Create a project and a first application (~4 min)

- Start new project **MAB** (Mobile Application Backend, or BAM reversed): `django-admin.py startproject mab .` (Note the trailing '.' character)
- Go to `mab`: `cd mab`
> **NOTE**: this way of organizing app inside project is not the default, but, in a smaller extends, it is the recommended way for django by [Two Scoops of Django](https://www.twoscoopspress.com/products/two-scoops-of-django-1-11), the best book in the world about good practise with django.
- Create a first app **Library** (that will store and manage BAM books): `django-admin.py startapp library`
- Add `'mab.library'` to the `INSTALLED_APPS`, in the settings.py
```python
INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'mab.library'
]
```
- Go back to root of the project: `cd ~/Code/django_formation`
- Migrate the database: `python manage.py migrate`
> **NOTE**: In order to keep the method of operation simple, we use the built in sqlite adapters. You can read more to change it the [beautiful documentation](https://docs.djangoproject.com/en/1.11/ref/settings/#databases).

> **CHECK**:
> - Create the django super user: `python manage.py createsuperuser` and fill with a username and a `very-secret(™)` password
> - Run the server: `python manage.py runserver`
> - Access `http://127.0.0.1:8000` and see the "It worked page"
> - Access `http://127.0.0.1:8000/admin`, connect and see the admin page

## Add the first model (~5 min)

> **NOTE**: for editing Python code, I recommand using [Pycharm](insertLink). The debugging and auto complete features helps a lot. There is a community edition that is free to use.

- Open `mab/library/models.py` in Pycharm (or other, if you still resists).
- Lets start with a model `Author`: a model derive from `models.Model`, and for project documentation have a small docstring explaining what it is

```python
from django.db import models

class Author(models.Model):
    """Represent the author of a book"""
    pass
```

- Lets add the first fields in the Author models: there is a lot of available fields in `django.db.models` and even more available in plateform specifics modules (eg, [postgres array or json fields](https://docs.djangoproject.com/en/1.11/ref/contrib/postgres/fields/)), or in external plugins

```python
from django.db import models

class Author(models.Model):
    """Represent the author of a book"""
    first_name = models.CharField(
        max_length=20, 
        verbose_name="The first name of the author", 
        null=False, 
        blank=False
    )
    last_name = models.CharField(
        max_length=20, 
        verbose_name="The first name of the author",
        null=False,
        blank=False
    )
    created = models.DateTimeField(
        auto_now_add=True, 
        verbose_name="The creation date of the author in database"
    )
    modified = models.DateTimeField(
        auto_now=True, 
        verbose_name="The modification date of the author in database"
    )
```

> **NOTE**: it is a good practice to add created and modifed fields to models, and add a verbose name.


- Create auto-magicaly the migrations: `python manage.py makemigrations`
- Migrate the database: `python manage.py migrate`

> **CHECK 1**:
> - Launch `python manage.py showmigrations`

> **CHECK 2**:
> - Install https://sqlitestudio.pl
> - Open the migrated sqlite file
> - See the new table

## Complete with other models (~1 min)

- Edit the `models.py` file so it looks like:

```python
from django.db import models

SUBJECTS_ENUMS = (
    ('front', 'Front End'),
    ('back', 'Backend'),
    ('devops', 'Dev-Ops'),
    ('lean', 'Lean'),
    ('agile', 'Agile'),
)


class Author(models.Model):
    """Represent the author of a book"""
    first_name = models.CharField(max_length=20, verbose_name="The first name of the author", null=False, blank=False)
    last_name = models.CharField(max_length=20, verbose_name="The first name of the author", null=False, blank=False)
    created = models.DateTimeField(auto_now_add=True, verbose_name="The creation date of the author in database")
    modified = models.DateTimeField(auto_now=True, verbose_name="The modification date of the author in database")

    def __str__(self):
        # The name of the object
        return f"{self.first_name} {self.last_name}"


class Book(models.Model):
    title = models.CharField(max_length=40, null=False, blank=False, verbose_name="The title of the book")
    isbn = models.CharField(max_length=13, null=False, blank=False, verbose_name="The ISBN number")
    author = models.ForeignKey(Author, null=False, verbose_name="Link to the author of the book")
    subject = models.CharField(max_length=10, choices=SUBJECTS_ENUMS, null=False, blank=False, verbose_name="Main subject of the book")
    buy_date = models.DateField(verbose_name="The date when BAM buy the book")
    created = models.DateTimeField(auto_now_add=True, verbose_name="The creation date of the author in database")
    modified = models.DateTimeField(auto_now=True, verbose_name="The modification date of the author in database")
    
    def __str__(self):
        # The name of the object
        return f"{self.title}"
```

- Make migrations and migrate

## Generate the admin (~1 min)

- Open admin.py

```python
from django.contrib import admin
from .models import Author, Book

admin.site.register(Author)
admin.site.register(Book)
```

> **CHECK**:
> - Access `http://127.0.0.1:8000/admin`, connect and see the admin page


## Customize the admin (~3 min)

- You can edit the admin to profit from django powerful admin

```python
from django.contrib import admin
from .models import Book, Author


# The inline would list for every author the books he made
class BookInline(admin.TabularInline):
    model = Book
    readonly_fields = (
        'title',
        'isbn',
        'author',
        'subject',
        'buy_date',
        'created',
        'modified'
    )

    def has_add_permission(self, request, obj=None):
        return False

    def has_delete_permission(self, request, obj=None):
        return False


class AuthorAdmin(admin.ModelAdmin):
    # the field on edition page
    # (xxx, yyy), will put xxx and yyy on the same line
    fields = (
        ('first_name', 'last_name'),
        ('created', 'modified')
    )
    # the not editable field
    readonly_fields = (
        'created',
        'modified'
    )
    # pagination control on the list
    list_per_page = 10
    # fields available for full text search
    search_fields = ('first_name', 'last_name')
    # name of the list column
    list_display = ('first_name', 'last_name')
    # inlines
    inlines = (BookInline,)


class BookAdmin(admin.ModelAdmin):
    # the field on edition page
    # (xxx, yyy), will put xxx and yyy on the same line
    fields = (
        ('title', 'isbn'),
        'author',
        'subject',
        'buy_date',
        ('created', 'modified')
    )
    # the not editable field
    readonly_fields = (
        'created',
        'modified'
    )
    # pagination control on the list
    list_per_page = 10
    # Extra date selector
    date_hierarchy = 'buy_date'
    # fields available for full text search
    search_fields = ('title', 'subject')
    # name of the list column
    list_display = ('title', 'subject', 'buy_date', 'author')
    # filter side bar in list page
    list_filter = ('subject',)

admin.site.register(Author, AuthorAdmin)
admin.site.register(Book, BookAdmin)
```

> **CHECK**:
> - Access `http://127.0.0.1:8000/admin`, connect and see the admin page

## Install REST Framework (~5 min)

- Run `pip install djangorestframework`
- Save the dependencies to a lockfile: `pip freeze > requirements.txt`
- Add `'rest_framework'` to your `INSTALLED_APPS` setting.
```python
INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'mab.library',
    'rest_framework'
]
```
- Add django rest framework urls, in `urls.py` (optional)
```python
from django.conf.urls import url, include
urlpatterns = [
    ...
    url(r'^api-auth/', include('rest_framework.urls', namespace='rest_framework')),
    ...
]
```

## Write your first rest API (~10 min)

- Create a file `rest.py` in the same folder as `models.py`
- Copy the following
```python
from rest_framework import routers, serializers, viewsets
from .models import Book

# Serializers define the API representation.
class BookSerializer(serializers.HyperlinkedModelSerializer):
    class Meta:
        model = Book
        fields = ('title', 'subject', 'buy_date')

# ViewSets define the view behavior.
class BookViewSet(viewsets.ModelViewSet):
    queryset = Book.objects.all()
    serializer_class = BookSerializer

# Routers provide an easy way of automatically determining the URL conf.
router = routers.DefaultRouter()
router.register(r'books', BookViewSet)
```

- Add router urls, in `urls.py`
```python
from django.conf.urls import url, include
from django.contrib import admin
from mab.library import rest

urlpatterns = [
    url(r'^', include(rest.router.urls)),
    url(r'^api-auth/', include('rest_framework.urls', namespace='rest_framework')),
    url(r'^admin/', admin.site.urls),
]

```

# Next steps

- [GraphQL with django](http://docs.graphene-python.org/projects/django/en/latest/tutorial-relay/)
- [Screencast of django](https://godjango.com/)
- [Best Book ever for django](https://www.twoscoopspress.com/products/two-scoops-of-django-1-11)
- [Awesome Django](http://awesome-django.com/#awesome-django)