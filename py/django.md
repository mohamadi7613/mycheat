# Django


## MVT (Model-View-Template)

1. Models:    - Represent the data structure (database tables)
2. Views:     - Handle HTTP requests and return responses and how to interact with the db through ORM  [acts like controller in MVC]
3. Template:  - Use Django Template Language (DTL) 



## Install django

```bash
python -m venv myvenv          # create a virtual env named myvenv      # conventional name = env
myvenv/bin/activate            # activate virtual env in windows
source myvenv/bin/activate     # activate virtual env in linux
pip install django              # we can install django globally or in a virtual env
```


## Django commands

```bash
pip install django                    # its better to use virtual env for installing instead of globall instalation
python -m django --version            # django version
django-admin                         # make sure django is installed
django-admin startproject myproj          # first command for create a folder project (do not use dash)     # conventional name = backend
python manage.py startapp IMDB_app        # create app (we can have multiple apps inside main project) [add this "IMDB_app" in INSTALLED_APPS]
python manage.py runserver                # start server
python manage.py runserver 8080            # start server on port 8080
python mange.py createsuperuser            # create super user for admin page
python mange.py shell                      # run shell inside the project
python manage.py shell -c "from myapp.models import MyModel; print(MyModel.objects.count())"
python mange.py test                      # run tests
python manage.py collectstatic            # collect static files into "STATIC_ROOT" (use this production)
python manage.py clearsessions            # delete expired sessions (use this with a cronjob)

# migration
python manage.py makemigrations         # Create migrations      # run this after changing the structure of models [for method not req]
python manage.py migrate                # Apply migrations        # migrations convert model into sql query
python manage.py showmigrations         # Show migrations         
python manage.py migrate myapp          # Migrate specific app
python manage.py migrate myapp 0001    # Rollback migration
# if you have a syntax error in other parts of your code (like views) then migration will not work
```

### makemigration error 
if you do not run this command after changing your models, every request to that model gets error + you can not access admin panel
error: please enter the default value as valid python
solutions: 1. add default value: `default="some value"` 2. add `null=True` 3. one-off defualt value (use None if its nullable)

### when migrate?

1. Initial setup of a new project
    + Run python manage.py migrate to create the default Django tables (auth_user, sessions, admin_log, etc.)
2. creating or modifying models


### How to read errors?
duration


### Multiple apps

+ Built-in apps (admin, auth, sessions)
+ Creating multiple apps within a single project promotes modularity, scalability, maintainability and  better Team Collaboration
+ it avoids monolithic code, making debugging and testing easier.
+ Each app handles a `specific functionality` which is reusable and independent (e.g., users, blog, news, payments, analytics, chat, store).
+ A project can have many apps, and apps can be reused in other projects. (Don’t make one app for every model.)
+ `Reusibility`: A well-designed users app can be reused across multiple projects.
+ E-commerce Website:
    1. accounts → login, signup, profiles
    2. products → product catalog, categories
    3. cart → shopping cart
    4. orders → checkout, invoices
    5. payments → integration with PayPal/Stripe
    6. reviews → product reviews


### User admin page


## Flow of Djnago

1. Setup Project
    `python -m venv venv` → Activate → `pip install django` → `django-admin startproject projectname` → `cd projectname`

2. Create App
    `python manage.py startapp appname` → Add to `INSTLLED_APPS= []` in settings.py

3. Define Model
    Edit models.py → `python manage.py makemigrations` → `migrate` -> register: `admin.site.register(Book)`

4. Setup Admin
    `python manage.py createsuperuser` → Register model in admin.py (app folder)

5. Create View
    Edit views.py with logic and template rendering

6. Configure URLs
    Create appname/urls.py → Include in project's urls.py

7. Make Template
    Create templates/ folder → Add HTML files with Django tags

8. Run Server
    `python manage.py runserver` → Visit 127.0.0.1:8000

9. Forms
    Create forms.py → Update view to handle POST data


## Django code



### Model

+ `Model` is a Python class that represents a database table
+ we can have a Model with equal name in different apps (there is no conflict)
+ after creating a model: 
    1. make migrations 
    2. migrate 
    3. register the model in the admin file: `admin.site.register(Book)`

```python
class Book(models.Model):                # inherit from models.Model

    # 1. Common Field Types

    title = models.CharField(max_length=100)                        # max_length is required for charField
    description = models.TextField()                                      # TextField is for long text and not limited by default
    numbers = models.IntegerField()                         # -2,147,483,648 to 2,147,483,647   # 32-bit signed integer (2^31 - 1)
    numbers = models.PositiveIntegerField()                 # values from 0 to 2,147,483,647
    numbers = models.SmallIntegerField()                    # values from -32,768 to 32,767    # 2^16 - 1    # PositiveSmallIntegerField()
    numbers = models.BigIntegerField()                      # values from -9,... to 9,223,372,036,854,775,807  # PositiveBigIntegerField()
    price = models.DecimalField(max_digits=10, decimal_places=2)      # up to 10 digit, 2 decimal ---> 999.99, 77777.99
    is_bestselling = models.BooleanField(default = True)              # default value for boolean filed is False in djagno by default
    created_at = models.DateTimeField(auto_now_add=True)              # timestamps as Python datetime.datetime objects
    # auto_now_add= True ----> automatically set to the current time when the object is created
    # auto_now= True ------> automatically  set to the current time when the object is updated
    # if you set auto_now_add or auto_now, they shouldn't be included in ModelForms
    birthday_date = models.DateField()
    opening_time = models.TimeField()
    email = models.EmailField(max_length=300)                   # it has Automatic validation and Default max length is 254 characters
    url = models.URLField(max_length=300)               # it has Automatic validation and default max length is 200
    slug = models.SlugField()                           # slug HarryPotter ==> harry-potter  # allowed: letters + numbers + dash + underscore
    file = models.FileField(upload_to='uploads/')
    img = models.ImageField(upload_to='uploads/')
    tags = ArrayField(models.CharField(max_length=50), blank=True, default=list)    # just in psql 
    tags = models.JSONField(default=list)                                           # trick for having array in ohter dbs

    # 2. Relational Fields

    id = models.UUIDField(default=uuid.uuid4, editable=False)     # non-sequential, unique identifiers in 32 char can be used as primary key
    id = models.AutoField()                             #  Special IntegerField that auto-increments start with 1 (used for primary keys)
    author = models.ForeignKey(Author, on_delete=models.CASCADE, related_name="books")          # many to one (one book can have one author)
    address = models.OneToOneField(Address, on_delete=models.CASCADE)                            # one to one  
    country = models.ManyToManyField(Country)                                                    # do not need on_delete
    # related_name can be use inside shell when you revese query on Author example: ramin.books.all()
    # we do not need related_name in onetoone, it create automatically by djagno
    #  on_delete is required and you must explicitly specify it
    on_delete = models.PROTECT               # Prevent deletion of referenced object    
    on_delete = models.CASCADE               # Delete the referenced object
    on_delete = models.DO_NOTHING            # Do nothing, database may raise error
    on_delete = models.SET_NULL              # Set the referenced object to null
    on_delete = models.SET_DEFAULT           # Set the referenced object to the default value
    on_delete = models.SET(0)                # Set the referenced object to a value. in this exmpale 0
    # on_delete for author means: delete all books that has this author

    # 3. Field Options

    title = models.CharField(max_length=100, null=False, blank=False)           # max_length is required for charField
    default = ''             # empty string is default value of CharField, TextField, EmailField, URLField, SlugField
    default = None           # None is default value for lots of Field Types like DateField, IntegerField, etc.
    null = True               # if you don't specify a defult value or don't set null=True, Django will raise an error
    blank = True              # blank means field can be blank and empty string is accepted in forms in frontend
    null = False               # null means field can be null porgramatically in db
    editable = True            # editable means field can be editable in admin panel
    unique = True              # unique means field must be unique in db
    db_index = True              # db_index creates index in db to make it faster
    verbose_name = "Book Name"   # verbose_name is used in admin panel 
    help_text="Enter your name"     # help_text is used in admin panel as a placeholder of forms
    choices=[('male', 'Male'), ('female', 'Female'), ('other', 'Other')]         # choices is used in forms

    # 4. validators

    from django.core.validators import MinValueValidator, MaxValueValidator
    rating = models.IntegerField(
        default=100,
        max_length=4,
        validators=[
            MinValueValidator(3),                                           # length validators
            MaxValueValidator(4),                                             # validate numbers and strings
            RegexValidator(r'^[0-9]+$', message="Only digits are allowed")   # regex validator
            FileExtensionValidator(['pdf', 'docx'])                          # file validators
            MinValueValidator(date(1900, 1, 1)),                             # date validator
            MaxValueValidator(date.today())                                   # date validator
        ]
    ) 
    
    # 5. Model bulit-in methods
    
    def __str__(self):                               # Human-readable representation for 1. print object 2. admin page
        return f"{self.title} - {self.rating}"      # by defualt: "Book object(1)"

    def save(self, *args, **kwargs):                          # override save method
        self.slug = slugify(self.title, allow_unicode=True)    # add logic befoe/after saving
        super().save(*args, **kwargs)
    
    def delete(self, *args, **kwargs):                        # override delete method
        super().delete(*args, **kwargs)                         # add logic before/after deleting

    def get_absolute_url(self):                             # use this method in templates for redirect
        return reverse("named-url", args=[str(self.id)])

    @property                                                # property methods
    def is_in_stock(self):                                  # good for date operations
        return self.number > 0

    
    # 6. Metadata

    class Meta:                                 # special settings for additional information in admin page
        db_table = "book"                        # change table name
        verbose_name = "Address"                 # override singular name
        verbose_name_plural = "Address Entries"   # override plural name
        default_related_name = 'products'         # change default related name for related_name in onetoone
        ordering = ['-created_at', 'title']       # Default sorting for queries
        indexes = [models.Index(fields=['title'])]  # add index to db for faster search
```

### Model relations example

1. OneToOneField: One author can have only one address
2. ManyToManyField: Many authors can have many artiles
3. ForeignKey: One author can have many books



### admin

+ first create an admin: `python manage.py createsuperuser`

```py
from django.contrib import admin                                 # import your model from your app 
from book.models import Book                                    # from app_name.models import model_name
# register your model so you can import data into db using admin panel or see the list
# BookAdmin: Customise Book list view and Book Form view in admin panel
class BookAdmin(admin.ModelAdmin):                                      # name is up to you and not display in panel
    # values should be list or tuple

    # 1. list options
    list_display = ("title","author","rating","is_bestselling")       # display fileds as a column in list view
    list_filter = ("rating","is_bestselling")                         # sidebar filter in the right
    list_editable = ("author",)                                      # editable fields in list view
    search_fields = ('title', 'author')                                # add a search bar in panel with fulltext search
    ordering = ('-published_date',)                                   # Default ordering

    # 2. form options
    fields = ('title', 'author', 'summary')                           # fields in form layout (add and edit)
    readonly_fields = ("id",)                                         # noteditable fields showing in form view and list view
    prepopulated_fields = {"slug": ("title",)}                       # generate dynamiclly from other fields [ it can not be readonly_fields]
    exclude = ('slug',)                                              # exclude fields from form
    filter_horizontal = ('tags',)                                     # Better UI for ManyToMany

admin.site.register(Book)                                        # 1. Register normally
admin.site.register(Book, BookAdmin)                             # 2. Register with admin class
@admin.register(Book)                                            # 3. Register using decorator
class BookAdmin(admin.ModelAdmin):
    list_display = ('title', 'author')
```


### URLS

+ `path convertor` is a pattern to capture values from URLs and convert them to an appropriate data type and then passing them to view functions
+ Built-in Path Converters: `int`, `str`, `slug`, `uuid`, `re`
+ you can create your own path convertors
+ `dynamic path segment`: variable parts of a URL which we can get them as arguments of view functions

```py
# IMDB_app/urls.py                # Inside the app folder (IMDB_app), create a file called urls.py
urlpatterns = [
    path('home/', views.home,),                             # regular url
    path('home/', views.home, name='home'),                 # named url or Namespacing Urls   # we can access this url in templates or views
    path("",views.BookView.as_view(), name="articles"),    # if you use classBase views you need as_view() function
    path("suburl/",include("appname.urls")),                   # specifiy suburl and import another URLconf module (Nested url)
    path('old/', RedirectView.as_view(url='/new/')),        # redirect old url to new    

    # dynamic url patterns
    path('book/<int:id>/', views.book_detail),             # <int> matches any integer and "id" is a variable that we get in view function
    path("number<int:id>", views.func),                    # we can concatenate variables
    path("<str:article>", views.func),                     # article is a variable that we can get it in view like: func(req, article)
    path("<slug:article>", views.func),                   # slug validation  (a-z, 0-9, -, _)
    path("<uiid:article>", views.func),                   # uuid format like 550e8400-e29b-41d4-a716-446655440000
    path("<path:article>", views.func),                   # Like str, but also matches "/" like /a/b/c
    path("<article>/", views.func),                      # full dynamic path segment without type    # without slash in the end it gets error
    re_path(r'^articles/$', views.func),                # re_path for regex

    # ordering with dynamic + static
    path("articles/2003/", views.special_case_2003),                # special handling
    path('articles/<int:year>/', views.year_archive),                # order is important when we have dynamic + static
]

# myproject/urls.py
urlpatterns = [
    path('admin/', admin.site.urls),              # access admin panel with this url
    path('', include('IMDB_app.urls')),
]
```

###### Error Handling in Urls
```py
# urls.py                                                  # add this at the end of file (urls.py)
handler404 = 'myapp.views.custom_404_view'               #  when users request non-existent pages
handler500 = 'myapp.views.custom_500_view'              #  set DEBUG = False in production to see your custom error pages
```

###### Static files and media files
+ read more in later section, for now just know where to put them
```py
urlpatterns = [
    # ... your URLs ...
] + static(settings.STATIC_URL, document_root=settings.STATIC_ROOT) \
  + static(settings.MEDIA_URL, document_root=settings.MEDIA_ROOT)
```

##### Common url patterns
```py
path('', views.home, name='home')                                # Homepage
path('post/<int:pk>/', views.post_detail, name='post_detail')   # Detail View
path('posts/', views.post_list, name='post_list')                # List View
```



### VIEWS

1. Function-Based Views (FBV): 1. better Flexibility 2. good for full control 3. good for simple views
2. Class-Based Views (CBV):    1. better Reusability 2. good for complex views (handling GET,POST ,...)
    1. Define CBV
    2. Generic Class-Based Views(GCBV): 
        1. TemplateView
        2. ListView
        3. DetailView
        4. CreateView
        5. UpdateView
        6. DeleteView


```py
# views.py
def BookView(request, article):                   # 1. Function base view 
    if request.method == "POST":                  # article is a variable comes from url (dynamic url parameter)
        return HttpResponse("Hello")              # HttpResponse is a response method

class BookView(View):                             # 2. Class base view
    def get(self, request):                       # method for get method
        query = request.GET.get('q', '')          # access Query Parameters (GET Requests)
    def post(self, request):                      # method for post method       


# response methods
HttpResponse("Text")                                            # basic text response which returns string
HttpResponse("Not Found", status=404)                            # With status code
HttpResponse("<h1>HTML</h1>", content_type="text/html")          # With content type
HttpResponseNotFound("Not found")                 # 404         # Error Responses
HttpResponseForbidden("Access denied")            # 403 
HttpResponseBadRequest("Invalid request")         # 400
HttpResponseRedirect(reverse(articles))           # redirect to reverse function [named url]
HttpResponseRedirect("/article")                  # redirect to hardcode url
JsonResponse(data_list)                           # return data_list as json  # list(Book.objects.values())
JsonResponse(list(Book.objects.values()))         # convert dict to json: 1. convert '' to "" 2. convert int to str , 3. convert True to true
JsonResponse([1, 2, 3], safe=False)                 # Safe=False for non-dict objects                 
JsonResponse({'error': 'Not found'}, status=404)     # return error message with status code
FileResponse(open('file.pdf', 'rb'))                  # return file
FileResponse(open('file.pdf', 'rb'), as_attachment=True, filename='doc.pdf')          # filename for download
StreamingHttpResponse(generatorFunc())                  # return a generator function (yield)
raise Http404("Not found")             # Debuge in stteing.py should be true in development and in production should have  a 404.html file 
render(request, "template.html", {"age":45})        # Render template with context
render_to_string("<div>hi</div>")               # from django.template.loader import render_to_string
redirect('/some/url/')                          # Redirect to URL
redirect('view-name')                     # Redirect to view name (url named)
Response({'key': 'value'}, status=201, headers={'X-Custom': 'value'})          # DRF Response (Django REST Framework)
```

### QuerySet

+ `QuerySet` are database queries in django ORM
+ queryset is an object that can be constructed, chained, and executed to retrieve objects from your database
+ you can use queryset in django shell
+ Django's ORM is designed to work with multiple relational databases: oracle, mysql, postgres
+ Django's ORM is not designed for: MongoDB, Redis, Cassandra, Neo4j

```py
# 1. basic query
Book.objects.get(id=1)                      # Get a single unqiue object (raises DoesNotExist or MultipleObjectsReturned)
Book.objects.get(title="foo")              # Get a single object by sql query
Book.objects.all()                # query set of  model instances (__str__ method)
Book.objects.count()              # number of objects
Book.objects.first()                         # Returns first by ordering
Book.objects.last()                         # Returns last by ordering
Book.objects.filter(title="foo")                         # Return matching records
Book.objects.exclude(title="foo")                         # Return non-matching records
Book.objects.exists(title='foo')               # check if exists     
Book.objects.filter(title='foo').exists()               # check if exists
Book.objects.filter(rating="11").filter(...)              # chaning

# 2. Ordering
Book.objects.all().order_by("title")                # order by title Ascending
Book.objects.all().order_by("-title")                # - for Descending
Book.objects.order_by("title", "price")                # Combined ordering
Book.objects.order_by("title").reverse()                # Flip current ordering
Book.objects.order_by('?')[:5]                     # Get 5 random records (warning: slow on large tables)

# 3. Slicing                                         # usecase: pagination
Book.objects.all()[0:5]                           # Get first 5 records
Book.objects.all()[0:5:2]                         # Get first 5 records with step 2
Book.objects.all()[::-1]                          # Reverse order

# 4. Serialization
Book.objects.values()                           #  Get dictionaries instead of model instances
Book.objects.values_list('id', flat=True)         # Get tuples of values (flat=True for single field)

# 5. Field Lookups
Book.objects.filter(title="foo")                         # Return matching records

# ===== BASIC COMPARISONS =====
Book.objects.filter(title__exact="public speaking")     # Exact match (case-sensitive)    # WHERE field = value
Book.objects.filter(title__iexact="Public Speaking")    # WHERE field ILIKE value         # Case-insensitive exact match
Book.objects.filter(score__in=[1, 2, 3])                # WHERE field IN (1, 2, 3)        # Value is in a list

# ===== NUMERIC RANGES =====
Book.objects.filter(field__gt=5)                 # Greater than                        # WHERE field > 5
Book.objects.filter(field__gte=5)                # Greater than or equal               # WHERE field >= 5
Book.objects.filter(field__lt=10)                # Less than                           # WHERE field < 10
Book.objects.filter(field__lte=10)               # Less than or equal                  # WHERE field <= 10
Book.objects.filter(field__range=(1, 10))        # Value between range (inclusive)     # WHERE field BETWEEN 1 AND 10

# ===== STRING MATCHING =====
Book.objects.filter(field__startswith='a')       # Case-sensitive starts with          # WHERE field LIKE 'a%'
Book.objects.filter(field__istartswith='a')      # Case-insensitive starts with        # WHERE field ILIKE 'a%'
Book.objects.filter(field__endswith='z')         # Case-sensitive ends with            # WHERE field LIKE '%z'
Book.objects.filter(field__iendswith='z')        # Case-insensitive ends with          # WHERE field ILIKE '%z'
Book.objects.filter(field__contains='Star')      # Case-sensitive contains             # WHERE field LIKE '%Star%'
Book.objects.filter(field__icontains='star')     # Case-insensitive contains           # WHERE field ILIKE '%star%'

# ===== RELATIONSHIPS =====
Book.objects.filter(author__name='John')         # Filter by ForeignKey relationship   # JOIN on author table
Book.objects.filter(categories__id__in=[1,2])    # Filter by ManyToMany relationship   # JOIN on M2M through table

# ===== NULL/BOOLEAN CHECKS =====
Book.objects.filter(field__isnull=True)          # Field is null                       # WHERE field IS NULL
Book.objects.filter(field__isnull=False)         # Field is not null                   # WHERE field IS NOT NULL
Book.objects.filter(field__isempty=True)         # Field is empty string               # WHERE field = ''


# 6. Relationships
book = Book.objects.get(id=1)                                  # Access related object directly
author = book.author                                          # ForeignKey access
books = Book.objects.filter(author__name='Hemingway')        # Filter by related field      # like join
publishers = Publisher.objects.filter(book__author__name='Hemingway')         # Publisher -> Book -> Author   # Multi-level Joins
books = Book.objects.select_related('author').all()         # Single query with JOIN   (Now book.author doesn't trigger another query)
books = Book.objects.prefetch_related('tags').all()        # Single query with JOIN     # 
author_books = author.books.all()                           # Reverse relation (related_name)
author_books = author.related_name.all()                    # filter with ForeignKey reverse [you should set related_name in model]
author_books = author.book_set.all()                       # filter with ForeignKey reverse
categories = book.categories.all()                         # ManyToMany access
book.publish_countries.add(countryobj)                       # add ForeignKey for many to many model bcz its a list


# 7. Q Objects (Complex Queries)
from django.db.models import Q                                                    
# | means or   # & menas and   # tilda (~) means not  # comma (,) means and         # we can use combination of Q objects
Book.objects.filter(Q(rating__lte=10) | ~Q(title__contains="Star"))                 # write Q object at the beginning
Book.objects.filter(Q(rating__lte=10) & Q(title__contains="Star"), author = "aa")   # if you write author='aa' at first you get error

# 8. Aggregates 
from django.db.models import Count, Sum, Avg, Max, Min
Book.objects.aggregate(Avg("rating"))                      # average of rating [retrun a dictionary]
Book.objects.aggregate(Count("rating"), Sum("rating"))    # output: {'rating__count': ..., 'rating__sum': ...}

# 9. Annotations
# Annotations compute summary values per object in the queryset (similar to SQL GROUP BY)
# Purpose: Extra data per each row (additional column)
authors = Author.objects.annotate(num_books=Count('book'))          # each Author has number of books
for author in authors:                             # select author_name, count(books) from authors group by author_name
    print(author.name, author.num_books)

# 10 F() Function                                            #  performing database operations at the SQL level
books = Book.objects.filter(title=F('author'))                # find books where title is equal to author name
books = Book.objects.filter(price__lt=F('cost') * 1.2)        # Find books where price is less than cost * 1.2
Product.objects.filter(id=1).update(views=F('views') + 1)       # Increment view count atomically  (Atomic Update)
books = Book.objects.annotate(total_value=F('inventory') * F('price'))    # Calculate total value 

# 10. Create
Book.objects.create(title="aa", rating=45)   # insert an instance using django models --> not use like class: Book(title="aa")
book, created = Book.objects.get_or_create(        # Get or create in one step (returns tuple: (object, created))
    title='The Old Man and the Sea',
    defaults={'author': 'ali'} 
)
```

#### Bulk operations

+ QuerySet is lazy
+ Running lots of individual `.save()` is slow because each one is a separate INSERT/UPDATE
+ `bulk APIs` let you touch thousands of rows with only a handful of SQL statements

```py
# 1. Bulk Create                                     # # Create all at once (returns created objects)
Book.objects.bulk_create(             
    [Book(title=t, price=p) for t, p in data],        # list comprehension for queryset
    batch_size=1000,          # The batch_size parameter controls how many objects are created in a single query
    ignore_conflicts=True,    # ON CONFLICT DO NOTHING (PostgreSQL, MySQL ≥8, SQLite ≥3.35)
)

# 2. Bulk Update
customers = Book.objects.filter(status='inactive')               # returns queryset, not the actual data yet (not execute yet)
for customer in customers:                                       #  Query runs here (when iterating)
    customer.status = 'active'
    customer.last_updated = timezone.now()

updated_count = Customer.objects.bulk_update(               # Update all at once (returns number of updated rows)
    customers,                                          # queryset
    ['status', 'last_updated'],                         # fields
    batch_size=500
)

# 3. QuerySet methods (bulk mechanism)
Book.objects.filter(rating__lte=10).update(rating=11)            #  QuerySet.update()
Book.objects.filter(rating__lte=10).delete()                      #  QuerySet.delete()
Book.objects.insert([                                             #  Insert
    {'title': 'book1', 'price': 99},
    {'title': 'book2', 'price': 149},
], ignore_conflicts=True)
```

#### Lazy evaluation query

+ django also use `cache` for returnung the result and not execute query again if you save that query in a variable
+ Django's QuerySets are `lazy` - they don't execute database queries until absolutely necessary

```py
# 1. lazy query evaluation
myQuery = Book.objects.filter(rating__lte=10))      # myQuery store the query definition but not execute it until print
print(myQuery)          # django now execute the query     # django uses cache for this query since we stored in a variable

# 2. QuerySet optimization                         # control which 'fields' are loaded immediately
Book.objects.defer('description')                     # Load everything except "description" now (exp: other fields are large)
books= Book.objects.only('title')                     #  Load only "title" field now; everything else is deferred
for book in books:                          # Accessing any other field triggers a second SQL query
    print(book.title)           # no extra query
    print(book.description)     # triggers separate SQL query
```

#### prefetch_related() 

+ for M2M relationships
+ `prefetch_related()` is  designed for "to-many" relationships (ManyToMany and reverse ForeignKeys)
+ `select_related()` is designed for "to-one" relationships. (ForeignKey, OneToOne)

```py
class Book(models.Model):
    title = models.CharField(max_length=200)
    tags = models.ManyToManyField('Tag', related_name='books')

class Tag(models.Model):
    name = models.CharField(max_length=50)

# 1. The Problem Without Prefetching          #  (N+1 problem) 1 for books + N for tags (inner)
books = Book.objects.all()                 # This creates 1 query for books + 1 query per book for tags
for book in books:                          # Django create 2 query:  1. SELECT * FROM book 2. SELECT * FROM author WHERE id=?
    print(book.tags.name)

# 2. Solution with prefetch_related()
books = Book.objects.prefetch_related('tags').all()        # just 2 query: 1. get all books 2. get all tags
```


#### select_related()

```py
class Author(models.Model):
    name = models.CharField(max_length=100)

class Book(models.Model):
    title = models.CharField(max_length=100)
    author = models.ForeignKey(Author, on_delete=models.CASCADE, related_name="books")

# 1. Without select_related
books = Book.objects.all()
for b in books:                       # N+1 query
    print(b.title, b.author.name)

# 2. With select_related
books = Book.objects.select_related("author")         # just 1 query
for b in books:
    print(b.title, b.author.name)

# related_name
ramin = Author.objects.get(name='ramin')
books = ramin.books.all()
```


### Data Access in View

```py
from django.shortcuts import get_object_or_404                      # get data from db and if not exist return "raise 404.html" file
def book_detail(request, pk):                                       # get_object_or_404 is alt for get with try except like
    book = get_object_or_404(Book, pk=pk)                            
    return render(request, 'books/detail.html', {'book': book})     # pass objects to template
    return render (request , "1.html" , {"age":45})                 # DTL  Django Tmplate Languages  
```

### templates

+ generating dynamic HTML
+ format is html
+ Separate presentation from business logic (views/models)
+ `Static files`: CSS, JS, images used in your site design
+ `Media files`: Uploaded by users (e.g., pictures, documents)
+ always use a web server (Nginx/Apache) in production! (for static and media files)

###### 1. Settings

```py
TEMPLATES = [
    {
        'BACKEND': 'django.template.backends.django.DjangoTemplates',
        'DIRS': [BASE_DIR / 'templates'],  # Global templates
        'APP_DIRS': True,  # Looks for app-specific templates
    }
]

STATIC_URL = '/static/'                             # URL prefix for static files
STATIC_ROOT = BASE_DIR / 'staticfiles'               # For collectstatic (in production) # we should use Nginx as a webserver for this
STATICFILES_DIRS = [BASE_DIR / 'static',  ]          # Global static files

MEDIA_URL = '/media/'                  # URL to access uploaded files
MEDIA_ROOT = BASE_DIR / "media"        # Where to store uploaded files
DATA_UPLOAD_MAX_MEMORY_SIZE = 5242880  # 5MB               # Maximum upload size (2.5MB default)
FILE_UPLOAD_MAX_MEMORY_SIZE = 5242880  # 5MB
```

###### 2. Template Structure

```css
/* Project-Wide Templates (Shared Layouts) */
myproject/
├── templates/           # Global templates (settings.TEMPLATES.DIRS)
│   ├── base.html        # Master layout   (Full HTML document)
│   ├── includes/        # Reusable components
│   │   ├── header.html  # # Fragment (<header>...</header>)
│   │   ├── footer.html    # Fragment
│   │   └── navigation.html
│   └── errors/
│       ├── 404.html
│       └── 500.html

/* App-Specific Templates */
myapp/
├── templates/
│   ├── base.html        # Master layout   (Full HTML document)
│   └── myapp/           # Namespaced under app name
│       ├── list.html    # Without namespacing (myapp/template.html), Django uses the first matching template found
│       ├── detail.html  # use namespacing to avoids naming collisions
│       └── partials/    # App-specific components
│           ├── card.html
│           └── form.html

/*  Project-Wide Static Files */
myproject/
├── static/  # Global static files (project-wide)
│   ├── css/
│   │   └── base.css
│   ├── js/
│   │   └── main.js
│   └── images/
│       └── favicon.ico
└── manage.py

/* App-Specific Static Files */
├── your_app/
│   ├── static/
│   │   └── your_app/  # App-specific static files (namespaced)
│   │       ├── css/
│   │       │   └── app.css
│   │       ├── js/
│   │       │   └── app.js
│   │       └── images/
│   │           └── logo.png
```

```py
return render(request, "myapp/list.html")                # Correct (namespaced)
return render(request, "list.html")                 # Avoid (non-namespaced) - may conflict with other apps
```

###### 3. DTL (djngo template language)

+ `jinja` is fatster than `DTL` and more powerful used by Ansible and Flask.

```py
# 1. Variables

{{ variable }}  # Renders a variable -->
{{ user.username }}  #  Access attributes -->
{{ list.0 }}  # Access list index -->
{{ dict.key }}  #  Access dictionary key -->

# 2. Filters (Modify variables)

{{ name|lower }}               #  Converts to lowercase -->
{{ value|default:"N/A" }}      # Fallback if empty -->
{{ date|date:"Y-m-d" }}          # Format date -->
{{ text|truncatechars:50 }}     # Limit text length -->

# 3. Tags (Control logic)

{% tag %} ... {% endtag %}               # Block tags -->
{% if condition %} ... {% endif %}        #  Conditionals -->
{% for item in list %} ... {% endfor %}  #  Loops -->
{% url 'view_name' arg %}                #  URL reversing -->

# 4. if-else
{% if user.is_authenticated %}
    <p>Welcome, {{ user.username }}!</p>
{% else %}
    <a href="{% url 'login' %}">Login</a>
{% endif %}

# 5. loop
<ul>
{% for book in books %}
    <li>{{ book.title }} ({{ book.author }})</li>
{% empty %}
    <li>No books found.</li>
{% endfor %}
</ul>

# 6. Loop Variables
{% for item in list %}
    {{ forloop.counter }}        #  1-based index -->
    {{ forloop.counter0 }}       # 0-based index -->
    {{ forloop.first }}          #  if first item -->
    {{ forloop.last }}          #  True if last item -->
{% endfor %}

# 7. block (Inheritance - base template)
<body>
	{% block content %}{% endblock %}
</body>

# 7. block (Inheritance - child template)
{% extends 'base.html'%}

{% block content %}
Hello, World!
{% endblock %}

# 8. include (base template)
<div>
{% include "includes/header.html" %}       # include is like inserting to HTML
</div>

# 8. include (child template)
<div> this is a card </div>


# 9. dynamic urls
<div> <a href="{% url 'namedurl' %}">{{item|title}}</a></div>
{% url 'blog:post_detail' post.id %}
<a href="{% url 'book_detail' pk=book.id %}">View Book</a>

# 10. forms
<form method="post" action="/users">    # default action is / and default method is GET
    {% csrf_token %}                         # creates a hidden input with a token in value attr
    {{ form.as_p }}                        #  Renders form as paragraphs -->
    <button type="submit">Submit</button>  # csrf_token is Required for POST requests
</form>
{{ form.username.errors }}              # Displays errors -->

# 11. Static
{% include "partials/header.html" %}                            # 
{% include "./var.html" with a="45" %}            # partial template with sending data

{% load static %}                                               # like "import" you need to load static file for using the in other tags
<link rel="stylesheet" href="{% static 'css/base.css' %}">        # CSS -->
<img src="{% static 'images/logo.png' %}" alt="Logo">             # Image -->
<script src="{% static 'js/main.js' %}"></script>                 #  JS -->

# 12. Tag Utilities
{% with total=items.count %}{{ total }}{% endwith %}
{% now "Y-m-d H:i" %}
{% verbatim %}{{ raw js }}{% endverbatim %}
```

##### HTML Content

1. Base HTML (Base Temple) ---->   full html tags
2. Partial HTML (Child Template) ---->  not full html  ---> full of DTL variables
3. HTML Fragments (Components) ---->  not full html ---> Often represent UI components (cards, buttons, modals)

```html
<!-- templates/base.html -->                  <!-- 1. Base Template -->
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>{% block title %}Default Title{% endblock %}</title>
</head>
<body>
    {% include "includes/navbar.html" %}
    {% block content %}{% endblock %}
</body>
</html>

<!-- templates/components/book.html -->         <!-- 2. Partial Template -->
{% extends "base.html" %}
{% block title %}Child Title{% endblock %}     <!-- order is not important -->
{% block content %}                             <!--just write DTL syntax in this file-->
    <article>                          <!-- No <html>, <head>, or <body> needed -->
        <h1>{{ post.title }}</h1>
        <div>{{ post.content }}</div>
    </article>
{% endblock %}

<!-- templates/includes/navbar.html -->        <!-- 3. HTML Fragments -->
<nav class="navbar">
    <a href="/">Home</a>
    <a href="{% url 'about' %}">About</a>
</nav>
```

### static files


```py
# 1. settings.py 
# 2. urls.py
from django.conf import settings

if settings.DEBUG:                       # For production only if DEBUG is False
    urlpatterns += static(settings.STATIC_URL, document_root=settings.STATIC_ROOT)
    urlpatterns += static(settings.MEDIA_URL, document_root=settings.MEDIA_ROOT)
    # urlpatterns = [path('/'),...] + static(settings.STATIC_URL, document_root=settings.STATIC_ROOT)          # another syntax

# 3. templates
{% load static %}
<link rel="stylesheet" href="{% static 'css/style.css' %}">
<img src="{% static 'images/logo.png' %}" alt="Logo">
```



#### Generic VIEW

+ `Generic Views` are pre-built class-based views that handle common web development patterns
+ we call them generic since: `from django.views.generic import ListView`

1. Display Views
    ListView: Displays a list of objects
    DetailView: Displays details for a single object
    TemplateView: Renders a template without any model context

2. Editing Views
    FormView: Displays and processes a form
    CreateView: Creates new model instances
    UpdateView: Updates existing model instances
    DeleteView: Deletes existing model instances

###### 1. TemplateView

+ simplest CBV for GET method perfected for static pages that don't need to interact with db

```py
# 1. basic usage
class HomePageView(TemplateView):
    template_name = "appName/home.html"     # the only required argument

# 2. with context
class HomePageView(TemplateView):
    template_name = "appName/home.html"
    extra_context = {                       # use these as variable in html template
        "first_name": "ali",                # {{first_name}} in template
        "last_name": "reza",
    }

# 3. override get_context_data()                     # we can override get_context_data() for all generic views
class BookView(TemplateView):    
    template_name = "1.html"       # 
    def get_context_data(self, **kwargs):             # send date into template similar to extra_context ={}
        context = super().get_context_data(**kwargs)  # get id from url (do not forget to add variable in urls.py)
        id = self.kwargs.get("id")                    # {{id}} in template   # there is no need to add to context
        context["message"] = "for insert into temolate"    # {{message}} in template
        return context
```

###### 2. ListView

```py
class BookListView(ListView):
    model = Book                            # the only required argument
    template_name = 'books/book_list.html'  # Optional (default: <app>/<model>_list.html)  {{books}} {{books.title}} 
    context_object_name = 'books'  # Optional (default: object_list)    #  get objects with name books in template  
    paginate_by = 15                   # Optional pagination   (default: 10 per page)
    ordering = ["-published_at"]        # Optional ordering     (default: newest first)

    def get_context_data(self, **kwargs):    # send date into template or get a variable from url
    def get_ordering(self):                # overrider ordering
    def queryset(self):                    # overrider queryset for how to fetching data
        base_queryset = super().queryset()
        return base_queyset.filter(rating__gte=10)          
# template variables: 1. {{object_list}} 2. {{page_obj}} 3. {{is_paginated}} 4. {{next_page}} 5. {{previous_page}} 6. {{object_list}}
```

###### 3. DetailView

+ `slug` is a URL-friendly, human-readable version of a string
+ It is used to create clean, SEO-friendly URLs
+ slug or pk ==> use in urlpatterns=[]
    + `<int:pk>`
    + `<slug:slug>`

```py
class BookDetailView(DetailView):    # Class base view for render a template for get method
    model = Book                   # get model 
    template_name = "1.html"       # optional (default: <app>/<model>_detail.html)
    context_object_name = "Book"   # Optional (default: object or lowercase model name)  # {{book.title}} in template
    queryset = Book.objects.filter(is_published=True)  # Only show published items
    slug_field = 'custom_slug'  # Default: 'slug'     # Specifies the database field for the slug lookup
    slug_url_kwarg = 'book_slug'  # Default: 'slug'     # Matches URL variable in urlpatterns=[]  <slug:book_slug>
    def get_object(self):            # override get_object
        obj = super().get_object(queryset)
        # add custome logic 
        return obj
```

###### 4. CreateView

+ `CreateView`, `UpdateView`, `FormView` have similar template syntax.

```py
class BookFormView(CreateView):       # saves data in db automatically
    model = Book                      # Required  # create a form view like db
    fileds = "__all__"               # Required  #  all fields in db  (or use `form_class`)
    fields = ['field1', 'field2']   # Fields to include in the form (or use `form_class`)
    template_name = '1.html'         # Default: 'yourapp/yourmodel_form.html'
    success_url = '/success/'        # Where to redirect after successful submission
    form_class = MyCustomForm         # no auto-generate form (create a class for your own form instead of `fields`)

    def get_initial(self):            # Adding Initial Data
        return {'status': 'draft'}     # Pre-fill 'status' field

    def form_valid(self, form):        # customize save logic and modifying form before saving
        form.instance.author = self.request.user
        return super().form_valid(form)

class MyCustomForm(forms.ModelForm):
    class Meta:
        model = Book
        fields = ["title", "content"]
        widgets = {                               # custome widgets
            "title": forms.TextInput(attrs={"class": "form-control"}),
        }
# template
# template variables: 1. {{form}} 2. {{form.fields}}
<form method="post">
    {% csrf_token %}
    {{ form.as_p }}             # form as paragraph 
    {{ form.as_ul }}             # form as list
    {{ form.as_table }}          # form as table
    <input type="submit" value="Save">
</form>
```

###### 5. UpdateView

+ very similar to CreateView

```py
class BookUpdateView(UpdateView):             # update/<int:pk>/
    model = Book  # Required
    fileds = "__all__"                          # Required  #  all fields in db  (or use `form_class`)
    fields = ['field1', 'field2']                    # Fields to include in the form (or use `form_class`)
    template_name = '1.html'  # Default: 'yourapp/yourmodel_form.html'
    success_url = '/success/'  # Where to redirect after update
    form_class = MyCustomForm  # no auto-generate form (instead of `fields`)
```

###### 6. DeleteView

```py
class BookDeleteView(DeleteView):     # delete/<int:pk>/
    model = Book  # Required
    template_name = '1.html'  # Default: 'yourapp/yourmodel_confirm_delete.html'
    success_url = reverse_lazy('success-view-name')  # Where to redirect after deletion

# template
<form method="post">
    {% csrf_token %}
    <input type="submit" value="Yes, delete">
</form>
```

###### 7. FormView

+ `FormView` is for handling forms that don't correspond to a model (unlike CreateView/UpdateView)
+ It's perfect for: 1. Contact form 2. search form
+ first we should create a form class using `forms.Form`
+ `forms.Form` is the base class for creating standard HTML forms in Django that aren't directly tied to a model

```py
class BookFormView(FormView):                      # FormView
    form_class = ContactForm                        # create this form using `forms.Form`
    template_name = "1.html"                       # send the form to template_name
    sucess_url = "/thanks"                           # redirect
    def form_valid(self, form, _FormT):
        form.save()                                # save to database   where??????
        return super().form_valid(form)

# 1. regular form
class ContactForm(forms.Form):
    user_name = forms.CharField(max_length=100)          # create an <input name=user_name /> and a label=User Name
    email = forms.EmailField()
    message = forms.CharField(widget=forms.Textarea)    #  override default widget

# 2. model form
class ContactForm(forms.Form):     
    class Meta:
        model = Book            # connect this form to Book model
        fields = "__all__"      # save all fields or use []
        exclude = ["title"]    # do not save these fields
        labels = {"author": "Author Name"}  # override label
        error_messages = {"author": {"required": "Please enter your name", "max_length": "Name too long"}}
```

###### 8. forms.Form in detail

```py
class ContactForm(forms.Form):                 # from django import forms

    # 1. form fields
    text = forms.CharField()
    message = forms.CharField(widget=forms.Textarea)   # Multi-line text
    number = forms.IntegerField()
    decimal = forms.DecimalField()
    price = forms.FloatField()
    boolean = forms.BooleanField()
    null_boolean = forms.NullBooleanField()                      # None/True/False
    date = forms.DateField(widget=forms.SelectDateWidget())
    email = forms.EmailField()
    choice = forms.ChoiceField(choices=[('A', 'Option A'), ('B', 'Option B'), initial='A'])
    file = forms.FileField()
    image = forms.ImageField()  # Requires Pillow library
    url_field = forms.URLField()

    # 2. form validation
    char_field = forms.CharField(
        max_length=100,          # Maximum length
        min_length=3,            # Minimum length
        required=True,           # Whether field is mandatory
        error_messages={"required": "Please enter your name", "max_length": "Name too long"}
        label='Name',            # Human-readable label
        initial='Default',       # Initial value
        help_text='Your name',   # Help text
        widget=forms.TextInput(attrs={
            'class': 'form-control',
            'placeholder': 'Enter name'
        })
    )

    integer_field = forms.IntegerField(
        min_value=1,
        max_value=100
    )

    decimal_field = forms.DecimalField(
        max_digits=5,           # Total digits (incl. decimals)
        decimal_places=2        # Digits after decimal point
    )

    boolean_field = forms.BooleanField(
        required=False,         # Typically not required for checkboxes
        label='I agree'
    )
    
    # 3. form widgets
    forms.TextInput()        # Standard text input
    forms.PasswordInput()    # Password field
    forms.Textarea()         # Multi-line text
    forms.CheckboxInput()    # Single checkbox
    forms.Select()           # Dropdown select
    forms.RadioSelect()      # Radio buttons
    forms.CheckboxSelectMultiple()  # Multiple checkboxes
    forms.SelectDateWidget() # Date dropdowns
    forms.FileInput()        # File upload
```

###### 9. function base view for form.Form

```py
# ContactForm is created by forms.Form
def BookView(request):                                     # function base view for custome validation
    # request.POST is a dictionary
    username = request.POST.username                       # access post elements from frontend
    username = request.POST.get('username')                # Safer than direct access


    # 1. regular form (non-model form)
    form = ContactForm(request.POST)                       # For non-model forms 
    if form.is_valid():                                    # check if form is valid
        username = form.cleaned_data["name"]                     # get cleaned data [validated data]
        email = form.cleaned_data["email"]
        # add your logic with this data
        return redirect('success-url')

    # 2. model form
    form = ContactForm(request.POST, instance=Book.objects.get(pk=1))    # update a model with form
    if form.is_valid():                                          # check with model validation
        form.save()                                            # Only works with ModelForm
        return redirect('success-url')


# template.html
<form method="post">
    {% csrf_token %}
    {{form}}     # create an input with a label, also will return error
    <input type="submit">
    {{form.username}}
    {{form.username.label_tag}}
    {{form.username.errors}}
</form>
```


###### 10. Upload media files  (forms.Form)

```py
# 0. settings.py   (media files)
# 0. urls.py       (meida files)

# 1. model in db
class ProfileModel(models.Model):
    # 1.1. folder path
    image = models.ImageField(upload_to="images/")
    image = models.FileField(upload_to="images/")     # set path in settings.py
    files = forms.FileField(widget=forms.ClearableFileInput(attrs={'multiple': True}))        # multiple files
    # 1.2. date path
    file = models.FileField(upload_to='uploads/%Y/%m/%d/')
    uploaded_at = models.DateTimeField(auto_now_add=True)

# 2. forms.Form
class MyForm(forms.Form):
    image = forms.ImageField(label="Image", help_text="Max. 5MB")
    image = forms.FileField()

# 3. function base view
def upload_view(request):                                      
    if request.method == 'POST':
        form = MyForm(request.POST, request.FILES)              # MyForm is created by forms.Form
        if form.is_valid():

            # Option 1: Save directly to model
            uploaded_file = ProfileModel(file=request.FILES['file'])
            uploaded_file.save()

            # Option 2: Process file manually
            file = request.FILES.file                                      # access file elements from frontend
            file = request.FILES['file']                                     # safer
            with open(f'some/path/{file.name}', 'wb+') as destination:       # store image in a file with binary method
                for chunk in file.chunks():
                    destination.write(chunk)

            # finally
            return redirect('success-page')


# 3. class based view
class UploadView(FormView):
    form_class = MyForm
    template_name = 'upload.html'
    success_url = '/success/'
    def form_valid(self, form):
        # Access file with self.request.FILES['file']
        return super().form_valid(form)

# 4. templates
<img src="{{ user.profile.avatar.url }}" alt="Avatar">
```



### Django shell

+ for testing queries

```py
# python manage.py shell
from app_name.models import Book      # app.models import className 
author = Author(first_name="aa", last_name="aa")       # create object
a = Book(title = "aa", rating = 45, author = author)   # create object with ForeignKey
a.save()                          # save data in db
a.delete()                       # delete data in db
```

### Django shull_plus

```bash
pip install django-extensions ipython           # install django-extensions and IPython
python manage.py shell_plus                     # Auto-imports all your models
```


### Authentication

###### 1. Auth: settings.py

```py
INSTALLED_APPS = [
    ...
    'django.contrib.auth',  # Core authentication
    'django.contrib.contenttypes',  # Required by auth
    ...
]

MIDDLEWARE = [
    ...
    'django.contrib.sessions.middleware.SessionMiddleware',
    'django.contrib.auth.middleware.AuthenticationMiddleware',
    ...
]

AUTH_USER_MODEL = 'myapp.CustomUser'  # Optional: for custom user models
LOGIN_URL = '/accounts/login/'  # Default: '/accounts/login/'
LOGIN_REDIRECT_URL = '/'  # Where to redirect after login
LOGOUT_REDIRECT_URL = '/'  # Where to redirect after logout
```

###### 2. Auth: User Model 

```py
from django.contrib.auth.models import User    # default user model

user = User.objects.create_user(            # Create user by queryset
    username='john',
    password='secret123',
    email='john@example.com'
)

# python manage.py createsuperuser
```

###### 2. Auth: User Model (custom user model)

```py
from django.contrib.auth.models import AbstractUser

class CustomUser(AbstractUser):
    phone = models.CharField(max_length=20)
    bio = models.TextField(null=True, blank=True)
    REQUIRED_FIELDS = ['email', 'phone']                 # REQUIRED_FIELDS for createsuperuser command
```

###### 3. Auth: built-in Views 

```py
from django.contrib.auth import views as auth_views
urlpatterns = [
    path('accounts/login/', auth_views.LoginView.as_view(), name='login'),
    path('accounts/logout/', auth_views.LogoutView.as_view(), name='logout'),
    path('accounts/password_change/', auth_views.PasswordChangeView.as_view(), name='password_change'),
    path('accounts/password_change/done/', auth_views.PasswordChangeDoneView.as_view(), name='password_change_done'),
    path('accounts/password_reset/', auth_views.PasswordResetView.as_view(), name='password_reset'),
    path('accounts/password_reset/done/', auth_views.PasswordResetDoneView.as_view(), name='password_reset_done'),
    path('accounts/reset/<uidb64>/<token>/', auth_views.PasswordResetConfirmView.as_view(), name='password_reset_confirm'),
    path('accounts/reset/done/', auth_views.PasswordResetCompleteView.as_view(), name='password_reset_complete'),
]
```

###### 4. Auth: Custome views

```py
class MyLoginView(auth_views.LoginView):
    template_name = 'myapp/login.html'
    redirect_authenticated_user = True  # Redirect if already logged in
    
    def form_valid(self, form):
        remember_me = form.cleaned_data.get('remember_me')
        if not remember_me:
            self.request.session.set_expiry(0)  # Session expires when browser closes
        return super().form_valid(form)
```

### Session

+ Django provides a session framework
+ `session` is an ongoing connection between a client and a server [even after shutdown]
+ `Session` is ideal for storing temporary, user-specific data that needs to persist across multiple requests.
    - User Authentication: 1. Authentication flags 2. Authenticated tokens 3. Permission flags
    - User Preferences: 1. Language 2. Theme 3. Timezone 4. Display preferences (list/grid view)
    - UI State: 1. Open/closed states of UI elements 2. Recently viewed items 3. Notification dismissal states 


+ HTTP is stateless → every request is independent.
+ How session works:
    1. Client Makes First Request: (logs in via django.contrib.auth)
        - No session exists yet
        - Django generates a unique session ID
    2. Server Response:
        - Session ID is sent to client (usually `as a cookie`)
        - Session data is stored server-side ( Session data is stored on the server)
    3. Subsequent Requests:
        - Client sends the session ID with each request
        - Django retrieves the corresponding session data

+ `session` is server-side but `cookie` is client-side

#### admin panel
+ you cannot see a table in admin panel for sessions by defult unlike tokens in DRF
+ for having a table in admin panel we should register `Session` model
```py
from django.contrib.sessions.models import Session
class SessionAdmin(ModelAdmin):
    def _session_data(self, obj):
        return obj.get_decoded()
    list_display = ['session_key', '_session_data', 'expire_date']
admin.site.register(Session, SessionAdmin)
```


###### 1. Session: settings.py

```py
SESSION_ENGINE = 'django.contrib.sessions.backends.db'      # Database-backed    # default  # Creates django_session table
SESSION_ENGINE = 'django.contrib.sessions.backends.file',    # File-based         # specify path: SESSION_FILE_PATH
SESSION_ENGINE = 'django.contrib.sessions.backends.cache',   # Cache-backed like redis
SESSION_ENGINE = 'django.contrib.sessions.backends.cached_db',  # Cache + DB fallback
SESSION_ENGINE = 'django.contrib.sessions.backends.signed_cookies',  # Cookie-based   # Data stored in client-side cookies (encrypted)

MIDDLEWARE = [                                                # in middleware order is very important
    ...                                                        # each HTTP request will be processed by these middlewares
    'django.contrib.sessions.middleware.SessionMiddleware',     # Every request gets a request.session object
    'django.contrib.auth.middleware.AuthenticationMiddleware'   # session should come before AuthenticationMiddleware
    ...                                                       # session works for both authenticated and anonymous users
]

INSTALLED_APPS = [
    'django.contrib.sessions',                                 # usually already present
]
SESSION_SERIALIZER = 'django.contrib.sessions.serializers.JSONSerializer'      # for security # Default
SESSION_SERIALIZER = 'django.contrib.sessions.serializers.PickleSerializer'

# Session settings
SESSION_COOKIE_NAME = 'mysessionid'           # Default: 'sessionid'
SESSION_COOKIE_AGE = 1209600                  # defaults: 2 weeks # in seconds 
SESSION_COOKIE_SECURE = True                  # HTTPS only (for production)
SESSION_COOKIE_HTTPONLY = True                # Prevent JS access
SESSION_COOKIE_SAMESITE = 'Lax'               # CSRF protection
SESSION_EXPIRE_AT_BROWSER_CLOSE = False       # Persistent sessions     # default: False
SESSION_SAVE_EVERY_REQUEST = True             # Save on every request  # default: False
SESSION_FILE_PATH = '/tmp/django_sessions/'   # For file-based session
```

###### 2. usage

```py
request.session.session_key                           # get session key
session_data = request.session.items()               # Get all session data from complex data
request.session['username'] = 'mohammad'              # Set a session variable   (store data in session)
username = request.session.get('username')            # returns None if not set  (get data from session)
username = request.session.get('username', 'guest')   # with default
request.session.set_expiry(3600)                      # set expiration time  # in minutes
request.session.get_expiry_age()                      # get expiration time
request.session.cycle_key()                           # get new session key
del request.session['username']                        # delete specific session variable
request.session.delete("key")                          # delete data from session
request.session.clear()                                # delete all session variables but keep the session
request.session.flush()                                   # Completely delete the session with its data
request.session.pop('user_id', None)                  # Safe delete
request.session['user_prefs'] = {                     # Complex data (automatically serialized)
    'theme': 'dark',
    'language': 'en',
    'notifications': True
}
```

###### 2. Session: views

```py
# 1. set seesion
def login_view(request):
    if request.method == "POST":
        request.session['user_id'] = 42              # set session (Store the user's ID session)
        request.session.set_expiry(3600)            # Expires in 1 hour
        return redirect('dashboard')
    else:
        return render(request, 'login.html')

# 2. get session
class SessionView(View):
    def get(self, request):
        counter = request.session.get('counter', 0) + 1
        request.session['counter'] = counter
        return HttpResponse(f"Visit count: {counter}")

# 3. show session with template
class MyDetail(DetailView):
    model = Book
    template_name = "1.html"
    def get_context_data(self, **kwargs):
        context = super().get_context_data(**kwargs)
        context["session_key"] = self.request.session.session_key       # <p>{{ session_key }}</p>  
```


### Session and Authentication

```py
from django.contrib.auth import authenticate, login, logout

# 1. login
def login_view(request):
    if request.method == 'POST':
        username = request.POST['username']
        password = request.POST['password']
        user = authenticate(request, username=username, password=password)
        
        if user is not None:
            login(request, user)               # This creates the session and stores the authenticated user's ID in the session
            return redirect('dashboard')         # login() Sets session cookie in the response

# 2. logout
def logout_view(request):
    logout(request)                           # Flushes the session: 1. delete session cookie 2. delete session data 
    return redirect('home')
```

### Checkgin authenticated user

######## 1. check in Views
```py
if request.user.is_authenticated:
    # Logged-in user
else:
    # Anonymous user
```
######## 2. check in templates

```py
{% if user.is_authenticated %}
  Welcome, {{ user.username }}!
{% else %}
  Please log in.
{% endif %}
```

######## 2. check globally in middleware

```py

```

#### Django vs MERN Stack


1. Django (Server-Side Sessions)
    - Stateful: Server maintains session state
    - Built-in: Sessions are core Django functionality
    - Secure by default: CSRF protection, session fixation protection
    - Database/Cache-backed: Persistent server-side storage

2. MERN (Token-Based/Stateless)
    - Stateless: Server doesn't maintain session state
    - JWT/Cookie-based: Typically uses tokens (JWT) or client-side sessions
    - Manual security: Developers must implement security measures
    - Client-side storage: Tokens stored in localStorage/cookies

------------------------------------------------------------------------------

1. Django Flow
    1. User submits credentials
    2. Server validates and creates session
    3. Server sends session ID in HTTP-only cookie
    4. Subsequent requests include session cookie
    5. Server validates session on each request

1. MERN Flow
    1. User submits credentials
    2. Server validates and creates JWT
    3. Server sends JWT (cookie or response body)
    4. Client stores token (cookie/localStorage)
    5. Subsequent requests include token (header/cookie)
    6. Server validates token on each request



### Import in Djnago cheat sheet


```py
# 0. Core
from django.contrib import messages
from django.conf import settings
from django.core.exceptions import ObjectDoesNotExist

# 1. Http
from django.shortcuts import render, redirect, get_object_or_404
from django.http import HttpResponse, HttpResponseRedirect, JsonResponse
from django.urls import reverse, reverse_lazy

# 2. Views 
from django.views import View
from django.views.generic import ListView, DetailView, CreateView

# 2. Models & Database
from django.db import models
from django.db.models import Q, F, Count, Sum, Avg, Max, Min
from django.db.models.functions import Coalesce
from django.db.transaction import atomic

# admin
from django.contrib.admin import AdminSite
from django.contrib.admin.decorators import register
from django.contrib.admin.options import ModelAdmin

# 3. Forms
from django import forms
from django.forms import ModelForm, Form
from django.forms.widgets import TextInput, Select, CheckboxInput
from django.core.validators import validate_email, URLValidator

# 4. Authentication
from django.contrib.auth import authenticate, login, logout
from django.contrib.auth.models import User, Group
from django.contrib.auth.forms import UserCreationForm, PasswordChangeForm, PasswordResetForm
from django.contrib.auth.decorators import login_required
from django.contrib.auth.mixins import LoginRequiredMixin
```


### Common Response Codes

1. 200 OK - Successful GET/PUT/PATCH
2. 201 Created - Successful POST
3. 204 No Content - Successful DELETE
4. 400 Bad Request - Invalid data
5. 401 Unauthorized - Authentication failed
6. 403 Forbidden - No permission
7. 404 Not Found - Resource doesn't exist
8. 405 Method Not Allowed - Wrong HTTP method
9. 429 Too Many Requests - Throttled


### Settings

+ creates automatically by `django-admin startproject`

#### BASE_DIR

+ BASE_DIR points to the root directory of your Django project - the directory that contains manage.py.
+ `__file__` shows path to the current Python file

```py
from pathlib import Path
BASE_DIR = Path(__file__).resolve().parent.parent                 # .resolve() - Converts path to an absolute path
```

##### SECRET_KEY

+ when you create a new Django project it will automatically generate a SECRET_KEY
+ Django will refuse to start if SECRET_KEY is not set.
+ it is crucial to generate a strong, unique key for production 
+ The default key starts with 'django-insecure-' to remind you it's not production-ready.
+ If SECRET_KEY is exposed: ALL encrypted data is compromised
+ it is used to securely sign session cookies, reset passwords, CookieStorage, FallbackStorage, etc. 

```py
########## 1. django-environ                  #  pip install django-environ
import os                            # do not keep it in your settings.py
from pathlib import Path                            # secret manager ???????
SECRET_KEY = os.environ.get('DJANGO_SECRET_KEY')

######## 2. python-decouple                  #  pip install python-decouple
from decouple import config
SECRET_KEY = config('SECRET_KEY')

######## 3. dotenv
from dotenv import load_dotenv               # pip install python-dotenv
import os
load_dotenv()  # Load environment variables from .env file
SECRET_KEY = os.getenv('SECRET_KEY')
```


###### Generate New Key

```py
####### 1. Django's built-in function
from django.core.management.utils import get_random_secret_key
print(get_random_secret_key())

####### 2. secrets (recommended)
import secrets
secret_key = secrets.token_urlsafe(50)           # Generate a 50-character secret key
print(secret_key)

####### 3. openssl 
openssl rand -base64 32                         # generate a 32-byte (256-bit) random value
```

###### SECRET_KEY_FALLBACKS

+ Periodically update your SECRET_KEY
+ "Secret_Key Rotation" is the practice of periodically changing your application's secret key to enhance security
+ If SECRET_KEY changes, all users get logged out
+ strategy: 1. Time-Based Rotation 2. Version-Based Rotation
+ If SECRET_KEY is compromised: 
    - IMMEDIATELY rotate the SECRET_KEY
    - Force logout all users (sessions invalidated)
    - Invalidate all password reset tokens


```py
SECRET_KEY = "current-key-q3-2024"  
SECRET_KEY_FALLBACKS = ["previous-key-q2-2024"]
```

##### DEBUG

+ set to False in production
+ set DEBUG in .env file
+ When DEBUG = True, Django will show security warnings for:
    - Insecure passwords
    - Missing security middleware
    - Common security misconfigurations
+ When DEBUG = False:
    - Django hides debug info and shows a generic error page (500.html).
    - Set ALLOWED_HOSTS correctly
    - Configure static files manually (since Django won’t serve them automatically).
    - Provide custom error templates: (500.html, 404.html, etc.).

```py
from django.conf import settings
print(settings.DEBUG)                  # check current debug mode

if DEBUG:
    
```

##### INTERNAL_IPS

```py
# For local dev only
INTERNAL_IPS = [
    "127.0.0.1",
]
```

##### django-debug-toolbar


##### ALLOWED_HOSTS

+ When DEBUG = False, Django doesn’t work at all without a suitable value for ALLOWED_HOSTS.
+ This setting is required to protect your site against some CSRF attacks.

```py
ALLOWED_HOSTS = ['yourdomain.com', 'www.yourdomain.com']
```


#### CSRF

```py
CSRF_TRUSTED_ORIGINS = [
    'https://app.example.com',
]
```

##### 1. Cors

+ The user’s browser (people) never sends its own IP in the Origin header.
+ The browser sends the origin of the website that made the request (React server)
+ Therefore, the Django server only needs to allow the origin of the frontend, not every user.
+ CORS cares about the origin domain/IP:port of the frontend app

```py
CORS_ALLOWED_ORIGINS = [
    "https://yourfrontend.com",
]
CSRF_TRUSTED_ORIGINS = [
    "https://yourfrontend.com",
]
```
### whitenoise ??


#### URLs


#### WSGI

#### Gunicorn
+ Never use runserver in production.
+ Use Gunicorn, uWSGI, or Daphne behind Nginx.

```bash
gunicorn myproject.wsgi:application --bind 0.0.0.0:8000
```

#### Database

#### Templates

#### Internationalization




#### DRF


#### Logging


##### Deployment





