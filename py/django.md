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
python mange.py makemigrations            # run this when change the structure of models [method no req] (convert model into sql query)
python mange.py migrate                   # run all migrations and update database
python mange.py shell                      # run shell inside the project
python mange.py createsuperuser            # create super user for admin page
python mange.py test                      # run tests
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

## Django code



## 1. Model

+ `Model` is a Python class that represents a database table
+ after creating a model: 1. make migrations 2. migrate 3. register the model in the admin file
+ how to register: `admin.site.register(Book)`

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
    # on_delete options: 1. models.protect, 2. models.SET_NULL 3. models.SET_DEFAULT 4. models.CASCADE 5. models.SET(0)
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
    




    def __str__(self):                      # define method for print object from this class in console
        return f"{self.title} - {self.rating}"      # by defualt: Book object(1)

    def get_absolute_url(self):             # use this method in templates for redirect
        return reverse("named-url", args=[str(self.id)])

    def save(self, *args, **kwargs):       # override save method
        self.slug = slugify(self.title, allow_unicode=True)
        super().save(*args, **kwargs)

    class Meta:                    # special settings for admin page
        verbose_name = "Address"          # override singular name
        verbose_name_plural = "Address Entries"   # override plural name
    
```

#### URLS

+ Inside the app folder (IMDB_app), create a file called urls.py

```py
# IMDB_app/urls.py
urlpatterns = [
    path("<article>/", views.funct),    # dynamic path segment    # without slash in the end it gets error
    path("<str:article>", views.func),    # path convertor ==> func(req, article)
    path("<slug:article>", views.special_case_2003),    # slug foramt
    path("number<int:article>", views.special_case_2003, name="articles"),    # named url
    path("",views.BookView.as_view(), name="articles"),    # if you use classBase views you need as_view() function
    path("suburl/",include("app.urls"))      # specifiy suburl and import another URLconf module
]
# myproj/urls.py
urlpatterns = [
    path("",views.BookView.as_view(), name="articles"),
]
```


#### VIEWS

```py
######################### response
HttpResponseRedirect(reverse(articles))         # redirect to reverse function [named url]
HttpResponseRedirect("/article")         # redirect to hardcode url
HttpResponse("Hello")                     # return string
JsonResponse(data_list)                        # return data_list as json  # list(Book.objects.values())
# converting dict to json: 1. convert '' to "" 2. convert int to str , 3. convert True to true
HttpResponseNotFound("Not found")
raise Http404("Not found")             # Debuge in stteing.py should be true in development and in production should have  a 404.html file 
render_to_string("<div>hi</div>")               # from django.template.loader import render_to_string
render(request, "1.html", {"age":45})        #from django.shrtcuts  import render 

#######################views.py
def BookView(request):       # Function base view 
    if request.method == "POST":
        return HttpResponse("Hello")

class BookView(View):
    def get(self, request):       #Class base view for get method
    def post(self, request):      #Class base view for post method       
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

## Accessing data

+ you can use these in model or in shell

```py
Book.objects.all()                # query set of  model instances (__str__ method)
Book.objects.values()                # query set of dictionaries with all values
Book.objects.all().order_by("title")                # order by title [-title for descending]
Book.objects.count()              # number of objects
Book.objects.aggregate(Avg("rating"))    # average of rating. [retrun a dictionary]
Book.objects.create(title="aa", rating=45)   # create an instance using django models --> not use like class: Book(title="aa")
Book.objects.get(id=1)                      # find unique data (get(title="aa"))
Book.objects.filter(rating="11").filter(...)              # find multiple data (rating__lte=10) (title__contains="Star") [modifier]
Book.objects.filter(author__first_name="aa")    # filter with ForeignKey
ramin.book_set.all()                           # filter with ForeignKey reverse [find all ramins books]
ramin.related_name.all()                       # filter with ForeignKey reverse [you should set related_name in model]
bookobj.publish_countries.add(countryobj)        # add ForeignKey for many to many model bcz its a list
from django.db.models import Q              # write Query set with Or combination
Book.objects.filter(Q(rating__lte=10) | Q(title__contains="Star"))   # | means or and , means and
Book.objects.filter(Q(rating__lte=10) | Q(title__contains="Star"), author = "aa")   # if you write author for the first you get error
myQuery = Book.objects.filter(rating__lte=10))                        # myQuery store the query definition but not result bcz it is not executed yet [you can chain and this is good for performance] [print(myQuery) ==> django now run the query] [django also use cache for returnung the result and not execute query again if you save that query in a variable like myQuery]
a = get_object_or_404(Book, pk=1)      # alt for get with try except ==> get data from db and if not exist return[raise] 404.html file
```

## admin

```python
from django.contrib import admin         # from app_name.models import model_name
from book.models import Book            # import your model from your app
class BookAdmin(admin.ModelAdmin):    # name if up to you
    readonly_fields = ("id",)                       # read only field for noteditable fields
    prepopulated_fields = {"slug": ("title",)}    # prepopulated_fields for generate online in admin [ it can not be readonly_fields]
    list_filter = ("rating","is_bestselling")                       # filter list in the right
    list_display = ("title","author","rating","is_bestselling")       # display fileds as a column
admin.site.register(Book, BookAdmin)     # register your model so you can import data into db using admin panel
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


