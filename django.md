
<!--write: mohamadi_arch-->

# python venv
```bash
# alternative: poetry, pipenv, etc.
# env is installed by python by default
# your proj folder should be outside of env folder
python -m venv myname     # create a virtual env (a folder which contains inner folders and files and a config file)
# before install any package you should activate virtual env
source myname/bin/activate     # activate virtual env in linux
myname/bin/activate            # activate virtual env in windows
pip freeze                  # see list of installed packages in virtual env
pip freeze > requirement.txt          # create requirement.txt for sharing this proj with someone else
```


# Django commands

```bash
python -m django --version            # django version
django-admin                         # make sure django is installed
django-admin startproject myfolder  # first command for create a folder project
python manage.py startapp chanllenges_app        # create app (we can have multiple apps inside main project) [add this name in INSTALLED_APPS]
python manage.py runserver                # start server
python mange.py makemigrations            # run this when change the structure of models [method no req] (convert model into sql query)
python mange.py migrate                   # run all migrations and update database
python mange.py shell                      # run shell inside the project
python mange.py createsuperuser            # create super user for admin page
python mange.py test                      # run tests
```

### makemigration error 
error: please enter the default value as valid python
solutions: 1. add default value: `default="some value"` 2. add `null=True` 3. one-off defualt value (use None if its nullable)


### User admin page
1. active: when we add a new user inside admin panel just this field is checked
2. staff status: you can see admin panel   ===> staff member
3. superuser status: you can edit the admin panel

# Django code


#### URLS
```py
urlpatterns = [
    path("<article>/", views.funct),    # dynamic path segment    # without slash in the end it gets error
    path("<str:article>", views.func),    # path convertor ==> func(req, article)
    path("<slug:article>", views.special_case_2003),    # slug foramt
    path("number<int:article>", views.special_case_2003, name="articles"),    # named url
    path("",views.BookView.as_view(), name="articles"),    # if you use classBase views you need as_view() function
    path("suburl/",include("app.urls"))      # specifiy suburl and import another URLconf module
]
```

## Model
```python
#  a model is a Python class that represents a database table.
# after creating a model: 1. make migrations 2. migrate 3. register the model in the admin site
class Book(models.Model):      # inherit from models.Model
    title = models.CharField(max_length=50, null=False, blank=Ture, editable=False)   # max_length is required
    rating = models.IntegerField(validators=[MinValueValidator(1), MaxValueValidator(10)]) # validators
    is_bestselling = models.BooleanField(default = False)
    slug = models.SlugField(default ="", db_index=True, unique=True)  # slug HarryPotter ==> harry-potter db_index make it faster
    date = models.DateField(auto_now=True)               # auto_now (every time update) and auto_now_add (just add)
    id = models.AutoField()                                                                     # primary key
    author = models.ForeignKey(Author, on_delete=models.CASCADE, related_name="books")          # many to one (one book can have one author)
    address = models.OneToOneField(Address, on_delete=models.CASCADE)                            # one to one  
    country = models.ManyToManyField(Country)                                                    # do not need on_delete
    # related_name can be use inside shell when you revese query on Author ex: ramin.books.all()
    # we do not need related_name in onetoone, it create automatically by djagno
    # on_delete options: 1. models.protect, 2. models.SET_NULL 3. models.SET_DEFAULT 4. models.CASCADE 5. models.SET(0)
    # on_delete for author means: delete all books that has this author

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


class BookView(TemplateView):    #Class base view for render a temolate for get method
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
from book.models import Book      # app.models import className
author = Author(first_name="aa", last_name="aa")       # create object
a = Book(title = "aa", rating = 45, author = author)   # create object with ForeignKey
a.save()                          # save data in db
a.delete()                       # delete data in db
```

## Accessing data
you can use these in model or in shell

```py
Book.objects.all()                # query set of  model instances (__str__ method)
Book.objects.values()                # query set of dictionaries with all values
Book.objects.all().order_by("title")                # order by title [-title for descending]
Book.objects.count()              # number of objects
Book.objects.aggregate(Avg("rating"))    # average of rating. [retrun a dictionary]
Book.objects.create(title="aa", rating=45)   #another way to save
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
from django.contrib import admin
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


