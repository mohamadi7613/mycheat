# Django


## Install django

```bash
python -m venv myvenv          # create a virtual env named myvenv
myvenv/bin/activate            # activate virtual env in windows
source myvenv/bin/activate     # activate virtual env in linux
pip install django              # we can install django globally or in a virtual env
```


## Django commands

```bash
pip install django                    # its better to use virtual env for installing instead of globall instalation
python -m django --version            # django version
django-admin                         # make sure django is installed
django-admin startproject myproj          # first command for create a folder project (do not use dash)
python manage.py startapp IMDB_app        # create app (we can have multiple apps inside main project) [add this "IMDB_app" in INSTALLED_APPS]
python manage.py runserver                # start server
python mange.py shell                      # run shell inside the project
python mange.py createsuperuser            # create super user for admin page
python mange.py test                      # run tests

# migration
python manage.py makemigrations         # Create migrations      # run this after changing the structure of models [for method not req]
python manage.py migrate                # Apply migrations        # migrations convert model into sql query
python manage.py showmigrations         # Show migrations
python manage.py migrate myapp          # Migrate specific app
python manage.py migrate myapp 0001    # Rollback migration
```

### makemigration error 
if you do not run this command after changing your models, every request to that model gets error + you can not access admin panel
error: please enter the default value as valid python
solutions: 1. add default value: `default="some value"` 2. add `null=True` 3. one-off defualt value (use None if its nullable)

### How to read errors?
duration


### User admin page
1. active: when we add a new user inside admin panel just this field is checked
2. staff status: you can see admin panel   ===> staff member
3. superuser status: you can edit the admin panel


## Django Raodmap

1. Setup Project
    `python -m venv venv` → Activate → `pip install django` → `django-admin startproject projectname` → `cd projectname`

2. Create App
    `python manage.py startapp appname` → Add to `INSTLLED_APPS= []` in settings.py

3. Define Model
    Edit models.py → `python manage.py makemigrations` → `migrate`

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



## Model

+ `Model` is a Python class that represents a database table
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

    # 2. Relational Fields

    id = models.UUIDField(default=uuid.uuid4, editable=False)     # non-sequential, unique identifiers in 32 char can be used as primary key
    id = models.AutoField()                             #  Special IntegerField that auto-increments start with 1 (used for primary keys)
    author = models.ForeignKey(Author, on_delete=models.CASCADE, related_name="books")          # many to one (one book can have one author)
    address = models.OneToOneField(Address, on_delete=models.CASCADE)                            # one to one  
    country = models.ManyToManyField(Country)                                                    # do not need on_delete
    # related_name can be use inside shell when you revese query on Author ex: ramin.books.all()
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


## admin

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


#### URLS

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
    path("suburl/",include("app.urls")),                   # specifiy suburl and import another URLconf module (Nested url)
    path('old/', RedirectView.as_view(url='/new/'))        # redirect old url to new    

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
# urls.py                                                  # add this at the end of file
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



#### VIEWS

1. Function-Based Views (FBV): 1. better Flexibility 2. good for full control 3. good for simple views
2. Class-Based Views (CBV):    1. better Reusability 2. good for complex views (handling GET,POST ,...)


```py
# views.py
def BookView(request, article):                   # 1. Function base view 
    if request.method == "POST":                  # article is a variable comes from url (dynamic url parameter)
        return HttpResponse("Hello")              # HttpResponse is a response method

class BookView(View):                             # 2. Class base view
    def get(self, request):                       # method for get method
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

## QuerySet

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
Book.objects.filter(field__exact=value)                         # 
Book.objects.filter(field__iexact=value)                         # 
Book.objects.filter(field__in=[1,2,3])                         # 
Book.objects.filter(field__gt=5)                         # 
Book.objects.filter(field__gte=5)                         # 
Book.objects.filter(rating__lte=10)                         # rating less than or equal
Book.objects.filter(field__startswith='a')                         # 
Book.objects.filter(field__istartswith='a')                         # 
Book.objects.filter(field__endswith='z')                         # 
Book.objects.filter(field__range=(1,10))                         # 
Book.objects.filter(field__icontains=value)                         # 
Book.objects.filter(title__contains="Star")                         # title contains
Book.objects.filter(author__first_name="aa")    # filter with ForeignKey

# 6. Relationships
ramin.book_set.all()                           # filter with ForeignKey reverse [find all ramins books]
ramin.related_name.all()                       # filter with ForeignKey reverse [you should set related_name in model]
bookobj.publish_countries.add(countryobj)        # add ForeignKey for many to many model bcz its a list

# 7. Q Objects (Complex Queries)
from django.db.models import Q              # write Query set with Or combination
# | means or   # & menas and   # tilda (~) means not  # comma (,) means and
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

# 10. Create
Book.objects.create(title="aa", rating=45)   # insert an instance using django models --> not use like class: Book(title="aa")
obj, created = Book.objects.get_or_create(field1=value1)        # Get or create in one step (returns tuple: (object, created))
```

#### Performance in ORM

+ Running lots of individual `.save()` is slow because each one is a separate INSERT/UPDATE
+ `bulk APIs` let you touch thousands of rows with only a handful of SQL statements
+ django also use `cache` for returnung the result and not execute query again if you save that query in a variable
+ Django's QuerySets are `lazy` - they don't execute database queries until absolutely necessary

```py
# 0. lazy query evaluation
myQuery = Book.objects.filter(rating__lte=10))      # myQuery store the query definition but not execute it until print
print(myQuery)          # django now execute the query     # django uses cache for this query since we stored in a variable

# 0. QuerySet optimization                         # control which 'fields' are loaded immediately
Book.objects.defer('description')                     # Load everything except "description" now (exp: other fields are large)
books= Book.objects.only('title')                     #  Load only "title" field now; everything else is deferred
for book in books:                          # Accessing any other field triggers a second SQL query
    print(book.title)           # no extra query
    print(book.description)     # triggers separate SQL query



# 1. Bulk Create                                     # # Create all at once (returns created objects)
Book.objects.bulk_create(             
    [Book(title=t, price=p) for t, p in data],        # list comprehension for queryset
    batch_size=1000,          # The batch_size parameter controls how many objects are created in a single query
    ignore_conflicts=True,    # ON CONFLICT DO NOTHING (PostgreSQL, MySQL ≥8, SQLite ≥3.35)
)


# 2. Bulk Update
customers = Book.objects.filter(status='inactive')               # Get queryset (not execute yet)
for customer in customers:                                       # Update in memory
    customer.status = 'active'
    customer.last_updated = timezone.now()

updated_count = Customer.objects.bulk_update(               # Update all at once (returns number of updated rows)
    customers,                                          # queryset
    ['status', 'last_updated'],                         # fields
    batch_size=500
)

# 3. QuerySet.update()
Book.objects.filter(rating__lte=10).update(rating=11)

# 4. QuerySet.delete()
Book.objects.filter(rating__lte=10).delete()

# 5. Insert
Book.objects.insert([
    {'title': 'book1', 'price': 99},
    {'title': 'book2', 'price': 149},
], ignore_conflicts=True)
```

### Access data view

```py
a = get_object_or_404(Book, pk=1)      # alt for get with try except ==> get data from db and if not exist return[raise] 404.html file
```


#### Template VIEW

```py
class BookView(TemplateView):    #Class base view for render a template for get method
    template_name = "1.html"       # random_name gets the template as render
    def get_context_data(self, **kwargs):    # send date into template
        context = super().get_context_data(**kwargs)
        id = self.kwargs.get("id")      # get id from url
        loaded_review = self.object          # load data from model 
        context["message"] = "for insert into temolate"
        return context

class BookListView(ListView):    #Class base view for render a template for get method
    template_name = "1.html"       # random_name gets the template as render
    model = Book                   # get model. by default read all 
    context_object_name = "books"       # get objects with name books instead of object_list in template by default
    def get_context_data(self, **kwargs):    # send date into template
    def get_ordering(self):                #overrider order
    def queryset(self):                    #overrider queryset for how to fetching data
        base_queryset = super().queryset()
        return base_queyset.filter(rating__gte=10)

class BookDetailView(DetailView):    #Class base view for render a template for get method
    template_name = "1.html"       # random_name gets the template as render
    model = Book                   # get model. by default read all
    # slug or pk ==> use in urlpatterns
    # in template you can use lowercase model name or "object"

class BookFormView(FormView):
    form_class = myForm      #send this data to template_name
    template_name = "1.html"
    sucess_url = "/thanks"    # redirect
    def form_valid(self, form, _FormT):
        form.save()          # save to database
        return super().form_valid(form)

class BookFormView(CreateView):    # saves data in db automatically
    model = Book         # create a form view like db
    fileds = "__all__"    # or a Model Form
    template_name = "1.html"
    sucess_url ="/thanks"   
```

### FORM

```py
##########################form.py
from django import forms
class MyForm(forms.Form):    # 1.regular form
    user_name = forms.CharField()        # create an <input name=user_name /> and a label=User Name
    user_name = forms.CharField(label="Your name", max_length=100, error_messages={"required": "Please enter your name", "max_length": "Name too long"})     # override error messages and label
    user_name = forms.CharField(widget=forms.TextArea(attrs={"placeholder": "Enter your name"})) # override default widget
    rating = forms.IntegerField(min_value=1, max_value=10) 

class MyForm(forms.Form):     # 2.model form like db
    class Meta:
        model = Book            # connect this form to Book model
        fields = "__all__"      # save all fields or use []
        exclude = ["title"]    # do not save these fields
        labels = {"author": "Author Name"}  # override label
        error_messages = {"author": {"required": "Please enter your name", "max_length": "Name too long"}}

#######validation
def BookView(request):             # function base view
    request.POST.username                       # access post elements from frontend
    form = MyForm()
    render(request, "1.html", {"form":form})
form = MyForm(request.POST)    # request.POST is a dictionary
form = MyForm(request.POST, instance=Book.objects.get(pk=1))    # update a model form
if form.is_valid(): # check if form is valid
    form.save()                      # just in model form not regular form
    name = form.cleaned_data["name"] # get cleaned data [validated data]
######################template.html
<form method="post">
    {% csrf_token %}
    {{form}}     # create an input with a label, also will return error
    <input type="submit">
    {{form.user_name}}
    {{form.user_name.label_tag}}
    {{form.user_name.errors}}
</form>
```


### Upload
```py
def upload(request):                         # view function
    request.FILES.image                      # access file elements from frontend

def store_file(file):                          # helper function for store image in a file with binary method
    with open(file.name, "wb+") as destination:
        for chunk in file.chunks():
            destination.write(chunk)

class MyForm(forms.Form):
    image = forms.ImageField()
    image = forms.FileField()

class ProfileModel(models.Model):
    image = models.ImageField(upload_to="images/")
    image = models.FileField(upload_to="images/")     # set path in settings.py
    MEDIA_ROOT = BASE_DIR / "media"        # settings.py
    MEDIA_URL = "user-media/"            # settings.py  [A=>B]
    urlpatterns = [] + static(settings.MEDIA_URL, document_root=settings.MEDIA_ROOT)   # connect path with url
```




# templates

```py
TEMPLATES =[ "DIRs" : [ BASE_DIR / "challenges/" / "templates"]]   ## way 1   [settings.py]
INSTALLED_APPS =[]            ## way 2 ==> register your app [settings.py]
render (request , "1.html" , {"age":45}) ## DTL tmplate languages  
context==> {{age}}
# tag {% for %} {% endfor %}
<div> <a href="{% url 'namedurl' %}">{{item|title}}</a></div>
{% block mycontent %}{% endblock %}  # base.html ==> inheritance
{% include "./var.html" with a="45" %}            # partial template with sending data


############################ static file
{% block css_files %}{% endblock %}                    # in base.html file
###
{% load static%}                                        # first you need to load static file
{% block css_files %}                                  # in inheritance file
    <link rel="stylesheet" href="{% static 'challenges\challenge.css' %}">
{% endblock %}
# if you want to load general css file ===> settign.py => STATICFILES_DIRS = [BASE_DIR / "static"]
<form method="post" action="/users">    # default action is / and default method is GET
    {% csrf_token %}    # creates a hidden input with a token in value attr
</form>
```

## Django shell
```py
from app_name.models import Book      # app.models import className
author = Author(first_name="aa", last_name="aa")       # create object
a = Book(title = "aa", rating = 45, author = author)   # create object with ForeignKey
a.save()                          # save data in db
a.delete()                       # delete data in db
```


### static files

+ `Static files`: CSS, JS, images used in your site design
+ `Media files`: Uploaded by users (e.g., pictures, documents)
+ always use a web server (Nginx/Apache) in production!

```py
# 1. settings.py
STATIC_URL = '/static/'                   # URL to access static files in browser
STATICFILES_DIRS = [BASE_DIR / "static"]  # Optional (for extra locations)
STATIC_ROOT = BASE_DIR / "staticfiles"    # Only for production (collectstatic)

# 2. urls.py
from django.conf import settings
from django.conf.urls.static import static

if settings.DEBUG:                       # For production only if DEBUG is False
    urlpatterns += static(settings.STATIC_URL, document_root=settings.STATIC_ROOT)
    # urlpatterns = [path('/'),...] + static(settings.STATIC_URL, document_root=settings.STATIC_ROOT)          # another syntax


# 3. templates
{% load static %}
<link rel="stylesheet" href="{% static 'css/style.css' %}">
<img src="{% static 'images/logo.png' %}" alt="Logo">
```

### Media Files

```py

MEDIA_URL = '/media/'  # URL to access uploaded files
MEDIA_ROOT = BASE_DIR / "media"  # Where to store uploaded files

from django.conf import settings
from django.conf.urls.static import static

urlpatterns = [
    # your other urls
]

if settings.DEBUG:
    urlpatterns += static(settings.MEDIA_URL, document_root=settings.MEDIA_ROOT)

from django.db import models

class Profile(models.Model):
    avatar = models.ImageField(upload_to='avatars/')
# templates
<img src="{{ user.profile.avatar.url }}" alt="Avatar">
```


### Session
session: an ongoing connection between a client and a server [even after shutdown]
```py
SESSION_COOKIE_AGE = 1200                   # 1200 min ,defulalt is 2 weeks settings.py
SESSION_EXPIRE_AT_BROWSER_CLOSE = True      # default is False

request.session["key"] = value              # store data in session
request.session.get("key")                  # get data from session
request.session.delete("key")               # delete data from session
class MyDetail(DetailView):
    model = Book
    template_name = "1.html"
    def get_context_data(self, **kwargs):
        context = super().get_context_data(**kwargs)
        context["session_key"] = self.request.session.session_key
```


