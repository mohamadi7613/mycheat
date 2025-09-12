

# Django Rest Framework

## MVT or MVC ?

+ DRF is neither a strict MVT nor MVC framework, but it's built on top of Django, which follows the MVT architecture.
+ DRF extends Django's MVT pattern for building APIs
+ In traditional MVC frameworks (like Ruby on Rails or Django's underlying architecture), the View and Controller are often tightly coupled within the same server-side framework, rendering HTML directly on the server.


## Django-core vs DRF


| Feature                | Django                        | Django REST Framework (DRF)     | Best Use Cases                             |
|------------------------|-------------------------------|---------------------------------|-------------------------------------------|
| Primary Purpose        | Full-stack web development    | API development                 | Django: Websites with UI, DRF: APIs       |
| Data Format            | HTML                          | JSON/XML                        | DRF for mobile/SPA backends               |
| Templates              | Built-in template system      | Not included                    | Django for server-rendered pages          |
| Admin Interface        | Complete admin panel          | Needs customization             | Django for admin interfaces               |
| Authentication         | Session-based                 | Token/JWT/OAuth                 | DRF for API authentication               |
| Data Serialization     | Manual context passing        | Serializer classes              | DRF for consistent data structures       |
| URL Routing            | URLconf patterns              | ViewSets + Routers              | DRF for automatic endpoint generation    |
| Pagination             | Django Paginator              | Built-in pagination styles      | DRF for API pagination                   |
| Frontend Compatibility | Server-side rendering         | Designed for SPAs               | DRF with JavaScript frameworks           |
| Performance            | Faster HTML delivery          | JSON processing overhead        | Django for content-heavy sites           |



## terminal commands
```bash
pip install djangorestframework
pip show djangorestframework           # be sure you do not using venv or poetry or ...
```

## Installation
```py
INSTALLED_APPS = [       
    'rest_framework',        # base requirement for django rest
    'appname',
]
```

# Structure
```py
myproject/                   # backend
│
├── manage.py
├── myproject/
│   ├── __init__.py
│   ├── settings.py
│   ├── urls.py            
│   └── wsgi.py
│
├──appname\
│    ├──api\
│    ├── ---views.py
│    ├── ---urls.py
│    ├── ---serializers.py
│    ├── ---permissions.py         # custome permissions
│
└── requirements.txt
```

### Browsabel API

```py
# 1. globally change
REST_FRAMEWORK = {                             # how to change the browsable api page in our browser? 
    'DEFAULT_RENDERER_CLASSES': (               # gobally change
        'rest_framework.renderers.JSONRenderer',          # pure json insted of browsable API
    )
}
# 2. for specific view
class BookList(generics.ListAPIView):                # for specific view   (not recommended)
    render_classes= [renderers.JSONRenderer]     
```

### Simple summary of DRF

```py
# 1. model
class Book(models.Model):                           # similar to django
    title = models.CharField(max_length=100)
    author = models.CharField(max_length=100)
    def __str__(self):
        return self.title
# 2. serializer
class BookSerializer(serializers.ModelSerializer):
    class Meta:
        model = Book
        fields = '__all__'
# 3. view
class BookList(generics.ListCreateAPIView):    
    queryset = Book.objects.all()               
    serializer_class = BookSerializer           
    permission_classes = [IsAuthenticated]
```

### Flow of DRF

```
Client (e.g., React/Android/HTTPie)
    ↓
URLConf / Router
    ↓
ViewSet / APIView (Authentication → Permissions → Throttling)
    ↓
Serializer (Validation + Conversion)
    ↓
Model (Query / Save / Update / Delete)
    ↓
Serializer (Data → JSON)
    ↓
Renderer (JSON)
    ↓
HTTP Response
```

## Serialization

+ after createing a `Model` we should create a `Serializer` for that model
+ `Serialization`: process of converting complex data types (exp: Django models or querysets) into JSON or XML
+ `Deserialization`: process of converting JSON into a Python object
+ ORM is something else and it connect your Python models to the database
+ serialization in DRF is similar to `JSON.stringify / JSON.parse` in JS
+ we have 2 types of serialization: 1. Regular serializer 2. ModelSerializer
+ In django-core for serialization we have 3 steps: 
    1. get a queryset 
    2. convert queryset to object or list 
    3. convert object to json using JsonResponse

#### 1. Regular serializer

+ reqgular serializer is for manual definition of all fields and validation logic

```py
# serializer.py
from rest_framework import serializers                 # with regular serializer we should map everything manually

class BookSerializer(serializers.Serializer):          # serializer is a class inherited from method 1. regular Serializer
    
    # 1. Common Field Types                            # similar to Field Types in Models

    name = serializers.CharField(max_length=100)                   # text field
    age = serializers.IntegerField()                                # 
    is_active = serializers.BooleanField()
    price = serializers.DecimalField(max_digits=5, decimal_places=2)
    metadata = serializers.DictField()
    tags = serializers.ListField(child=CharField())
    # these property names should be same as in model
    # if we dont mention a field of model in serializer, that field wont map and we can not see that in the output
    my_custome_filed = serializers.CharField(default="me", read_only= True)   # to_representation()

    #  2. Field Options                                               # serializer level validations

    id = serializers.IntegerField(read_only=True)                       # read_only means it can not be updated (default: False)
    name = CharField(required=False)                                  # Is field required? (default: True)
    password = CharField(write_only=True)                              # Field is write-only (default: False)
    active = BooleanField(default=True)                                   # default value
    middle_name = CharField(allow_null=True)                                # Allow None as value (default: False)
    user_email = EmailField(source='user.email')                            # Attribute name on model (another name filed)
    score = IntegerField(validators=[validate_score])                      # Custom validators
    rating = sesrializers.PositiveIntegerField(validators= [MinValueValidator(1), MaxValueValidator(5)])   # import django.core.validators
    name = CharField(error_messages={'blank': 'Please enter your name'})     # Custom error messages
    dob = DateField(label='Date of Birth')                                   # Human-readable label
    notes = CharField(help_text='Additional information')                     # Descriptive text

    # 3. Common Methods for customizing

    def to_internal_value(self, data):                          # override for write operation
        return super().to_internal_value(data)                # Get the value before validation and Transform data before validation

    def to_representation(self, instance):                  # override for read operation          
        rep = super().to_representation(instance)           # Get the value for representation and Transform data before returning response
        rep['my_custome_filed'] = "value"                              # adding custom filed
        return rep

    def create(self, validated_data):                    # create method for 'POST' method in order to deserializing data
        return Book.objects.create(**validated_data)      # using Model for save to database     
        # we must impleament these methods otherwise we get error for post request
        # validated_data gets data from queryset inside views:   serilizer = BookSerializer(queryset)
        # The **validated_data syntax unpacks the dictionary into keyword arguments

    def update(self, instance, validated_data):          # update method for 'PUT' method in order to deserializing data
        instance.name = validated_data.get('name', instance.name)   # update 'name' of instance if the 'name' existed in the instance
        instance.active = validated_data.get('active', instance.active)   # validated_data is the dict of new data
        instance.save()    # instance is the old object in the database
        return instance    # instance and validated_data comes from views: serilizer = BookSerializer(queryset, data= req.data)

    def validate_first_name(self, value):                      # Filed level validations   # read 'serializers validation'
        pass                                                # ['validate' + PROPERTYNAME]

    def validate(self, data):                            # Object level validations         # read 'serializer validations'
        pass                                               # def validate()
```


#### 2. ModelSerializer

The ModelSerializer class is the same as a regular Serializer class, except that:

It will automatically generate a set of fields for you, based on the model.
It will automatically generate validators for the serializer, such as unique_together validators.
It includes simple default implementations of .create() and .update().

```py
# 1. minimal
class BookSerializer(serializers.ModelSerializer):   
    class Meta:               # all required fields
        model = Book
        fields = '__all__'


# 2. with detail
class BookSerializer(serializers.ModelSerializer):   

    # 1. Field Options

    custom = serializers.SerializerCharField(read_only=True, default="custom")    # custom field  (static filed)
    full_name = serializers.SerializerMethodField(read_only=True)    # custom field (dynamic filed)   # get_full_name()
    first_name = serializers.CharField(mex_length=50)                # override fields in ModelSerializer like regular serializer
    user_email = serializers.EmailField(source='user.email')          # change name 
    # if you dont use read_only for custome fields it gets error for post request bcz this field is not in db
    # there is no need to override fields but you can do it like regular serializer

    # 2. Class Meta                        # required

    class Meta:                         # with ModelSerializer default Fields with default validatros will be mapped automatically like model
        model = Book                     # in regualr serializer we do not introduce the model
        fields = '__all__'               # all fields will be serialized
        fields = ['first_name', 'active']     # only these fields will be serialized 
        exclude = ['rating']             # these fields will not be serialized [we can not use 'exclude' with 'fields' together]         
        depth = 1                         # optioanl: depth for nested relations
        read_only_fields = ['created_at']   # optional: fields that are read-only
        extra_kwargs = {'password': {'write_only': True}}    # validation for additional field kwargs ??????????
       
    # 3. Common methods for customizing                 

    def get_full_name(self, obj):                        # SerializerMethodField  [ def 'get_' + 'filedname']
        return obj.first_name + " " + obj.last_name      # we access all properties with 'obj'
        # we should mention this filedname in meta class  fields = ['age', 'full_name']
        # we should introduce this field in 'Field Options' with 'SerializerMethodField'

    # there is no need to create() and update() anymore ????????????

    def validate_first_name(self, value):                      # Filed level validations   # read 'serializers validation'
        pass                                                # ['validate' + PROPERTYNAME]

    def validate(self, data):                            # Object level validations         # read 'serializer validations'
        pass                                               # def validate()
```

### 3. HyperLinkedModelSerializer
```py
class BookSerializer(serializer.HyperLinkedModelSerializer):   # very similar to ModelSerializer
    class Meta:             # insted of id, it creates a link 
       model: Book          # we need to add context={'request': request} when we initialise the serializer in View
       fields = '__all__'   # we need to cretae a specific url in urlpatterns with a specific View for hyperlink
```

### Serializer Validations

+ validatros is equal for both ModelSerializer and  regular serializer
+ validation only works for POST and PUT requests
+ we have 3 types of validations: 
    1. Serializer level validations
    2. Filed level validations 
    3. Object level validations 

```py
class BookSerializer(serializers.Serializer):          

    # 1. serializer level validations
    id = serializers.IntegerField(read_only=True)                 # Each 'Serializer Filed' has 'core arguments' like read_only, required, ...
    firstname = serializers.CharField(max_length=100)               # mostly in reqular serializers
    description = serializers.CharField(validators=[myfunction])    # validator function outside the class

    # 2. Field-level validation                           # def validate_fieldname()
    def validate_firstname(self, value):                      # Field level validations for specific property
        if len(value) < 3:                                   # value is the value of name attribute
            raise serializers.ValidationError("Name is too short")  # return a json {name: "Name is too short"}
        else:                                         # this validations only work for post or put requests (not get request)
            return value                                # firstname becomes required bcz it does not have a default value
        # the syntax structuse is like a boilerplate for all Field-level validations

    # 3. Object-level validation
    def validate(self, data):                                                           #  we access all properties
        if data['firstname'] == data['lastname']:                                      # data is obj or instance of model
            raise serializers.ValidationError("Firstame can not be same as lastname")   # send a json {non_field_errors: "Name can not ..."}
        else:                                   # the syntax structuse is like a boilerplate for all Object-level validations
            return data

def myfunction(value):                                                 # function outside the class
    if len(value) < 10:                                                 # no need for return in validatros function
        raise serializers.ValidationError("Description is too short")   # raise but not return
```


#### Serializer relations


##### 1. Depth

+ `depth` means the depth of relationships that should be traversed before reverting to a flat representation.

```py
class BookSerializer(serializers.ModelSerializer):
    class Meta:
        model = Book
        fields = ['id', 'title', 'author']
        depth = 1          # shows nested object instead of id  [makes trouble for post request]

# no depth
{
    "title": "Django Book",
    "author": 1
}
# with depth
{
    "title": "Django Book",
    "author": {
        "name": "ali",
        "age": "20"
    }
}
```


##### 2. reverse relationship

```py
# inside Book model: author = models.ForeignKey(Author, on_delete=models.CASCADE, related_name='books')
# Author has many Books, Book has one author (book has foreign key). we can show the list of all books of an author.

# 1. way 1 for reverse relationship
class AuthorSerializer(serializers.ModelSerializer):
    class Meta:
        model = Author                                        # by this way we see ids of books
        fields = ['id', 'name', 'age', 'books']             # books is related_name in Book model
        depth = 1                                             # show nested object (book)

# 2. custome way for reverse relationship
class AuthorSerializer(serializers.ModelSerializer):                        # books is related_name in Book model
    books = serializers.PrimaryKeyRelatedField(many=True, read_only=True)   # 1. return ids of books
    books = BookSerializer(many=True, read_only=True)                       # 2. return whole content of books (this called nested serializer)
    books = serializer.StringRelatedField(many=True)                        # 3. __str__ from Book model
    books = serializers.SlugRelatedField(many=True,read_only=True,slug_field='title')  # 4. specific field from Book model 
    books = serilaizer.HyperLinkRelatedFiled(many=True,read_only=True, view_name='from_urlpatterns')   # 5. link to Book model object(forien key)
    books = serializers.PrimaryKeyRelatedField(                # 1. we can use queryset for validations
        queryset=Book.objects.all(),                      # queryset is required for write operation (POST,...)
        many=True                                           # For many-to-many or reverse FK (default: False)
    )   # set eather queryset or read_only=True              # read_only means we can not update it with post method

    class Meta:                       
        model = Author               
        fields = '__all__'                                 # books is not a model field for author so no included in __all__
        fields = ['id', 'name', 'age', 'books']             #  do not forget to add books here
```

#### 3. Nested reverse relationship

+ inside Book model we have a foreign key for author, and inside author we have books (reverse relationship)

```py
class BookSerializer(serializers.ModelSerializer):   
    author_obj = serializer.SerializerMethodField()     #  custom field with SerializerMethodField
    class Meta:     
        model = Book        
        fields = '__all__'

    def get_author_obj(self, obj):                      # get_   
        return AuthorSerializer(obj.author).data        # return whole content of obj by passing the id
```


## Views

1. FBV
2. CBV:
    1. APIView
    2. mixins
    3. generics
    4. viewsets
    5. ModelViewSet

| Feature          | APIView           | GenericAPIView    | ViewSet            | ModelViewSet       | FBV                |
|------------------|-------------------|-------------------|--------------------|--------------------|--------------------|
| **Control**      | Full              | Moderate          | Convention-based   | Least (Auto CRUD)  | Full               |
| **Boilerplate**  | Most              | Less              | Least              | None (Auto impl.)  | None               |
| **URL Routing**  | Manual            | Manual            | Router             | Router             | Manual             |
| **HTTP Methods** | Manual impl.      | Mixin-based       | @action decorator  | Auto CRUD methods  | Manual impl.       |
| **Best For**     | Custom endpoints  | Standard CRUD     | Custom REST routes | Full auto CRUD API | Simple endpoints   |


### 1. FBV (function base view) 

```py
from rest_framework.response import Response               # most important thing you should know is useing @api_view
from rest_framework.decorators import api_view               # error: .accepted_renderer not set on Response

@api_view(['GET','PUT','DELETE'])                           # decorator is necessary for FBV   
def book_list(request,pk):                                  # FBV without decorator gives 'AssertionError' for .accepted_renderer
    if request.method == 'GET':                             # @api_view handles unsupported methods with a `405 Method Not Allowed` response.
        try:                                                 # if any books does not find we get error
            books = Book.objects.all()                       # Get a queryset of books in the form of model instance (complex data)
            book_list = Book.objects.values()               # Get qyueryset of books in the form of dictionary
            serializer = BookSerializer(books, many=True)   # Serialize the queryset to object  # use many when we have more than one data
            return Response(serializer.data)               # not set 'many=True' : Got AttributeError when attempting to get a value for field       
            return JsonResponse(books)                     # alternative way for serializer: we can use JsonResponse in django-core
        except ObjectDoesNotExist:              
            return Response({"error": "Books do not exist"}, status=404)

    if request.method == 'PUT':                                     # PUT is for update all fields
        book = Book.objects.get(pk=pk)                               # Get pk from url
        serializer = BookSerializer(book, data=request.data)         # Deserialize the data with (old data, new data) or (queryset, data)
        if serializer.is_valid():
            myvar = serializer.validated_data['active']               # we can access validated data using dictionary
            serializer.save()
            return Response(serializer.data)                         # status code is 200 by default
        else:
            return Response(serializer.errors, status.HTTP_400_BAD_REQUEST)

    if request.method == 'DELETE':
        book = Book.objects.get(pk=pk)                                # Get the book for deleting
        book.delete()                                                 # call delete from model
        return Response(status=status.HTTP_204_NO_CONTENT)            # 204 is for succuesfully no content
```

###### request object

```py
@api_view(['GET','PUT'])                           
def book_list(request,pk):
    print(request)                        # <rest_framework.request.Request: GET '/lib/books/?age=45'>
    print(dir(request))                   # all properties inside request
    print(request.method)                 # "GET", "POST"
    print(request.GET)                    # GET query parameters (like ?key=value)
    print(request.POST)                   # POST form-data (if method="POST")
    print(request.headers)                 # HTTP headers
    print(request.META)                   # 	Full metadata (IP, cookies, etc.)
    print(request.user)                   # 	Current logged-in user (or AnonymousUser)
    print(request.FILES)                  # 	Uploaded files 
    print(request.body)                  # 	Raw request body (for APIs)
    print(request.path)                   # Requested URL path (/some/path/)
```


### 2.1 APIView (CBV)

+ `APIView` is the base class in DRF for all class based views.
+ its very similar to FBV
+  It provides more control and flexibility compared to DRF's `higher-level views` (like GenericAPIView or ViewSets).

```py
from rest_framework.views import APIView                 # we dont need @api_view decorators like function based
class BookList(APIView):                                 # class base view inherit from APIView 

    def get(self, request):                            # GET method 
        books = Book.objects.all()                     # get all querysets
        serilizer = BookSerializer(books, many=True)   # serialize the queryset by passing the queryset
        return Response(serializer.data)                # status is 200 by default
        # many=True is required when serializing a queryset or list

    def post(self, request, pk):                        # POST method 
        print(request.data)                             # request.data is a dictionary of sending data 
        print(request.query_params.get('param'))         # accessing query params
        serilizer = BookSerializer(data=request.data)    # deserialize the data   # do not forget to use 'data='
        if serializer.is_valid():                       # if we want to create an object with serializer we should use 'data=' as an argument
            serializer.save()                            # this is a boilerplate syntax for POST, PUT, PATCH, DELETE
            return Response(serilizer.data)            
        else:
            return Response(serializer.errors, status=400)

    def put(self, request, pk):                          # Full update
    def patch(self, request, pk):                        # Partial update
    def delete(self, request, pk):                        # Delete
        book = Book.objects.get(pk=pk)
```


### 2.2 mixins (CBV)

+ The mixin classes provide specific view behavior by `action methods` not `handler methods`
+ in `mixin views` we dont need handlers like get(), post(), etc like `APIView`
+ `mixin views`  in browsable API creates 'HTML form' and 'Raw data'
+ 
+ we need to inherit from some mixin views:
    1. ListModelMixin               GET       List objects               .list()
    2. CreateModelMixin             POST      Create objects             .create()
    3. RetrieveModelMixin           GET       Retrieve singel object     .retrieve()
    4. UpdateModelMixin             PUT       Full update                .update()
    5. DestroyModelMixin            DELETE    Delete                     .destroy()
    6. PartialUpdateModelMixin      PATCH     Partial update             .partial_update()

+ list(), create(), retrieve(), and so on are action methods
+ GenericAPIView extends APIView (check DRF source)
+ DRF provides pre-combined views using these mixins called `Generic Views`
+ `mixins` have lots of repeated  boilerplate code, its better to use `Generic Views`

```py
# 1. example for .list() .create()
from rest_framework import mixins, generics
class BookList(mixins.ListModelMixin, mixins.CreateModelMixin, generics.GenericAPIView):
    # for using mixins we need to inherit from GenericAPIView in the last
    queryset = Book.objects.all()                    # queryset is a keyword for the class otherwise we should override the `get_queryset()` method
    serializer_class = BookSerializer                # serializer_class is a keyword for the class otherwise override the `get_serialiser()` method
                                                    # there is no need for passing something to serializer like APIView
    def get(self, request, *args, **kwargs):          # you should implement get() method for GET request and use action methods in that
        return self.list(request, *args, **kwargs)   # .list() is for list of objects and .retrive() is for single object
    def post(self, request, *args, **kwargs):         # .list() and .create() are action methods
        return self.create(request, *args, **kwargs)  # .create() .update() .partial_update() .destroy()

# 2. example for .retrieve()
class BookDetail(mixins.RetrieveModelMixin, generics.GenericAPIView):
    queryset = Book.objects.all()           
    serializer_class = BookSerializer                   # writing get() and post() method in mixins is a boilerplate
    def get(self, request, *args, **kwargs):            #  pk should send in http params
        pk = kwargs.get('pk')                           # there is no need for doing this (getting pk from url)
        return self.retrieve(request, *args, **kwargs)  # just use .retrieve() instead of geting pk from url
```


### 2.3 Generic Views (CBV)

+ generic views provide pre-built solutions for common API patterns
+ generic views:
    1. ListAPIView 
    2. CreateAPIView 
    3. RetrieveAPIView 
    4. UpdateAPIView
    5. DestroyAPIView

+  combination:
    1. ListCreateAPIView
    2. RetrieveUpdateAPIView
    4. RetrieveDestroyAPIView
    3. RetrieveUpdateDestroyAPIView

+ Generic views reduce the amount of boilerplate code needed to write rather than mixins
+ for example, ListCreateAPIView already have 'def get(self, req, *args, **kwargs)' section inside its implementation (check DRF source)

```py
# 1. list
class BookList(generics.ListCreateAPIView):    # from rest_framework import generics         
    queryset = Book.objects.all()               # there is no need for handler methods or action methods 
    serializer_class = BookSerializer           # there is no need for Response and HTTP_Code

# 2. detail
class BookDetail(generics.RetrieveUpdateDestroyAPIView):  # mixed one  # get(), update(), delete()
    queryset = Book.objects.all()                        # just send an id in http params, it will handle it
    serializer_class = BookSerializer                    # there is no need for .filter() or . get()

# 3. list
class ActiveBookListView(generics.ListAPIView):
    serializer_class = BookSerializer
    
    def get_queryset(self):                         # instead of 'queryset' we can override it for customazation
        return Book.objects.filter(is_active=True)  

# 4. list + create
class BookList(generics.ListCreateAPIView):    
    serializer_class = BookSerializer              # overwriting list()
    def get_queryset(self):                        # instead of 'queryset' we can override it for customazation
        pk = self.kwargs.get('pk')                 # queryset handles extracting pk from url params by default
        return Book.objects.filter(author=pk)      # we can add some logics for customization

    def perform_create(self, serializer):  # overwriting create()           # perform_update(), perform_destroy()
       pk = self.kwargs.get('pk')           # no need for request.data
       author = Author.objects.get(pk=pk)   # in seriliazer we need to exclude author bcz we get if from url (no need for mapping)
       serializer.save(author=author)      # pk is inside url not in request.data
```

### 2.4 ViewSets (CBV)

+ in generic views we should write multiple classes but in `viwesets` we can handle everything inside one class 
+ viewsets does not porvide `.get()` and `.post()` handlers, and instead porvides `.list()` and `.retrieve()` actions.
+ `ViewSet` in DRF is a high-level abstraction that combines the logic for multiple views into a single class
+ since `ViewSet` and `ModelViewSet` generate multiple urls for a single view class we should register the view class in `urls.py`


```py
from rest_framework import viewsets        
class BookViewSet(viewsets.ViewSet):  
    def list(self, request):                                   # 1. list
        books = Book.objects.all()             
        serializer = BookSerializer(books, many=True)
        return Response(serializer.data)

    def retrieve(self, request, pk):                           # 2. detail 
        book = Book.objects.get(pk=pk)
        serializer = BookSerializer(book)
        return Response(serializer.data)

    def create(self, request):                                 # 3. create for POST method
        serializer = BookSerializer(data=request.data)
        if serializer.is_valid():
            serializer.save()
            return Response(serializer.data)
    def update(self, request, pk):                             # 4. update for put method
    def partial_update(self, request, pk):                     # 5. patch method
    def destroy(self, request, pk):                             # 6. delete method

    # we can add more action methods using @action            # 7. custom action method
    @action(detail=False, methods=['get'])                    # detail=False operates on all objects like list()
    def sales(self, request):                                 # detail=True operates on single object with passing pk in url like retrieve()
        # Custom action logic                                 # methods=['get', 'delete'] we can apply multiple methods
        return Response({"data": "Sales report data"})       # url: http://127.0.0.1:8000/books/sales/     # generate a new url for the action
                                                             # url name comes from method name 
    
    @action(detail=True, methods=['post'], url_path='marked', url_name='sales-array', permission_classes=[IsAdmin])
    # url_path for customiseing url segment at the end after pk like /books/{pk}/marked insted default: /books/1/sales
    # url_name for url reverse name like in urls.py which used by reverse() function
```


### 2.5 ModelViewSet (CBV)

+ since `ViewSet` and `ModelViewSet` generate multiple urls for a single view class we should register the view class in `urls.py`

```py
# 1. ModelViewSet
class BookViewSet(viewsets.ModelViewSet):                # ModelViewSet provides all CRUD methods
    """
    A ViewSet for viewing and editing book instances.
    This single class provides all methods automatically:
    - List (GET /books/)
    - Create (POST /books/)
    - Retrieve (GET /books/1/)
    - Update (PUT /books/1/)
    - Partial Update (PATCH /books/1/)
    - Destroy (DELETE /books/1/)
    """
    queryset = Book.objects.all()
    serializer_class = BookSerializer

# 2. read only ModelViewSet
class BookViewSet(viewsets.ReadOnlyModelViewSet):         # ReadOnlyModelViewSet provides only list() and retrieve()
    queryset = Book.objects.all()                           # syntax of properties like generics
    serializer_class = BookSerializer
```


###### Routers (registering ViewSet urls)

+ after creating viewset we need to register it with routers in urlpatterns bcz it manages multiple urls at the same time

```py
from rest_framework import routers                               # routers help us to combine urls
router = routers.DefaultRouter()                                 # create a router
router.register('books', BookViewSet.as_view(), basename='book')    # register
urlpatterns = [                                                    # both /books + /books/12 are available by this way
    path('url/',BookListView.as_view(), name="book-list"),         # normal CBV url
    path('', include(router.urls))                                # empty string
]                                                                # we can add "-list" and "-detial" at the end of names_url for viewsets
```



### User Model

+ Django provides a built-in User model (django.contrib.auth.models.User)
+ `User Model` in Django is the core representation of user accounts in your application.
    1. Core Fields:     username, password, email, first_name, last_name
    2. Permissions:     
        a. is_active:       when we add a new user inside admin panel just this field is checked
        b. is_superuser:    he can edit the admin panel
        c. is_staff:        he can see admin panel   ===> staff member
    3. Timestamps:      date_joined, last_login
+ its recommended to create a separate app for 'User Model': `python manage.py startapp userapp`
+ its recommended since we can have separate 'urls', 'serializers' and 'views' for User Model
+ It handles:
    1. Authentication (login/logout)
    2. Authorization (permissions)
    3. User profile (using `User Model` in other models as field)
    - in this part we will focus on number3 : `User Model` in other models
    

```py
# 1. default User Model Key fields (method 1)
from django.contrib.auth.models import User

username = models.CharField(max_length=150, unique=True)
email = models.EmailField(blank=True)
password = models.CharField(max_length=128)  # Hashed
first_name = models.CharField(max_length=30, blank=True)
last_name = models.CharField(max_length=150, blank=True)
is_active = models.BooleanField(default=True)
is_staff = models.BooleanField(default=False)
is_superuser = models.BooleanField(default=False)


# 2. extend User model  (method 2)
class Profile(models.Model):
    user = models.OneToOneField(User, on_delete=models.CASCADE)
    birth_date = models.DateField(null=True, blank=True)


# 3. Custom User Model by AbstractUser (method 3)
from django.contrib.auth.models import AbstractUser                     # models.py
class CustomUser(AbstractUser):
# AbstractUser Keeps all fields, lets you add/change extra fields
# good method when you don’t want a username field at all.
    phone = models.CharField(max_length=15, blank=True)                  # Add phone field 
    profile_pic = models.ImageField(upload_to='profiles/', null=True)    # Add profile_pic field
    USERNAME_FIELD = 'email'                                            # Replace username with email
    REQUIRED_FIELDS = []                                                # Removes email from REQUIRED_FIELDS


# 4. Build your own user model from scratch with AbstractBaseUser (method 4)


# 5. settings.py
AUTH_USER_MODEL = 'userapp.CustomUser'  # Point to your custom model


# 6. User Model in other Models
from usersapp.models import CustomUser           # if you customise User Model
from django.contrib.auth.models import User    # import User Model
class Review(models.Model):                  # get user from /admin page for using in other models
    reviewer = models.ForeignKey(User, on_delete=models.CASCADE, related_name='reviews')      # we should change serializer too
    content = models.TextField()


# 7. View
class ReviewCreate(generic.CreateAPIView):        # using user model in views
    serializer_class = ReviewSerializer
    def perform_create(self, serializer):
        pk = self.kwargs.get('pk')
        book = Book.objects.get(pk=pk)
        reviewer = self.request.user            # get user from request
        user_review= Review.objects.filter(reviewer=reviewer, book=book)
        if user_review:
            raise ValidationError("You have already reviewed this book")
```


### Permissions

+ with `permissions` a stranger cannot 'access' your data (restriction)
+ `Permissions` determine whether a client should be granted access to a particular endpoint or database object.
+ `Authentication` identifies who is making the request (user) while `Permissions` determine what that user can do (access control)

+ permissions can apply in 2 level:
    1. view level (globally to all views or specific view)
    2. object level  (for specific objects)


#### permissions: 0. global level

+ `global permissions` applies to all DRF views by default but individual views can override the global policy
+ + django provides 4 types of permissions: 
    1. IsAuthenticated (log in) 
    2. IsAdminUser 
    3. IsAuthenticatedOrReadOnly (only GET request for not login users)  
    4. AllowAny (by default)

```py
REST_FRAMEWORK = {                                            # setting.py
    'DEFAULT_PERMISSION_CLASSES': [                          # you can set only one permission
        'rest_framework.permissions.AllowAny'                  # No restrictions (default)
        'rest_framework.permissions.IsAuthenticated'          # only accessed by logged in users
        'rest_framework.permissions.IsAuthenticatedOrReadOnly'         # Requires login for POST
        'rest_framework.permissions.IsAdminUser'            # Staff/superusers only
        'rest_framework.permissions.DjangoModelPermissions'  # Ties to Django auth  ???????????????
        'myapp.permissions.CustomPermission',                  # Custom permission   
    ]
}
```

#### permissions: 1. view level

```py
from rest_framework import permissions

# 1. CBV
class BookList(generics.ListAPIView):    # CBV generic class
    queryset = Book.objects.all()
    serializer_class = BookSerializer
    permission_classes = [permissions.IsAuthenticatedOrReadOnly]   # CRUD accessed by logged in users otherwise read only for everyone

# 2. FBV
@permission_classes([permissions.IsAdminUser])       # decorator for permission
@api_view(['GET'])                                   # decorator for method
def Booklist(request):                              # function base view
   pass 
```

#### permissions: 2. object level

+ `object-level permissions` checked after `view-level permissions` pass
+ `object-level permissions` are used to restrict access (not just to views) to individual objects
+ example:  allow only users to update their own posts
+ we have no built-in `object-level permissions` by default so we should write a custom permission for this level
+ in custome permissions `def has_object_permission` is responsible for this

```py
# permissions.py
from rest_framework import permissions                            # famous example
class IsOwner(permissions.BasePermission):                        # create a custome permission for object-level
    """
    Custom permission to only allow owners of an object to edit or delete it.
    """

    def has_object_permission(self, request, view, obj):         # object-level customising
        return obj.owner == request.user

# views.py
from rest_framework.permissions import IsAuthenticated
from permissions import IsOwner

class PostDetailView(RetrieveUpdateDestroyAPIView):       # this permission not works with ListAPIView or ListCreateAPIView
    queryset = Post.objects.all()                         # this permission works with RetriveAPIView, UpdateAPIView, DeleteAPIView
    serializer_class = PostSerializer                     # IsAuthenticated is a built-in permission for view-level permission
    permission_classes = [IsAuthenticated, IsOwner]       # check user login AND ownership
```

#### custome permissions

+ to impleant custome permission we can implement two methods: `has_permission` and `has_object_permission`
+ `has_permission` runs before the view is called and checks general permissions and only access to the req and view
+ `has_object_permission` is only called for object-level operations (retrieve, update, delete).
+ For list/create actions (like in ListCreateAPIView), DRF calls `has_permission` instead.


```
|api\
|----permissions\              # custome permissions
```

```py
from rest_framework import permissions              # we can inherit from BasePermission or pre built permissions

# example 1
class AdminOrReadOnly(permissions.IsAdminUser):    # my custome permission    # this example is very famouse 

    # 1. has_permission
    def has_permission(self, request, view):                                # no access to object
        base_permission = super().has_permission(request, view)             # check default permission inherited from parent
        admin_permission = bool(request.user and request.user.is_staff)     # check if the user is admin or not  (is_staff means admin)
        return admin_permission or request.method == 'GET'                  # admin or readonly

    # 2. has_object_permission
    def has_object_permission(self, request, view, obj):                    # check real life example in example 2
        if request == 'DELETE':                                             # we can customize for specific methods
            return True                                                     # request.user returns 'AnonymousUser' or username
        if request.user.username = "mohamad":                               # check specific users
        if request.user.IsAuthenticated:                                    # check authentication
        # has_permission runs before the view is called and checks general permissions and only access to the req and view but not object

# example 2
class ReviewUserOrReadOnly(permissions.BasePermission):      # has_object_permission runs after the view gets the specific object
    def has_object_permission(self, request, view, obj):     # object-level : permissions for a specific 'model' instance
        if request.method in permissions.SAFE_METHODS:          # SAFE_METHODS is GET
            return True                                        #  access only by owner otherwise read only
        else:                                                 # else means: POST, PUT, PATCH, DELETE
            return obj.reviewer == request.user           # check if the reviewer is the same as the user [access object level]
```

### Temporary login

```py
urlpatterns = [                                                # Authentication for testing
    path('api-auth/', include('rest_framework.urls'))         # create a temp login for normal users and superusers
]  
# http://127.0.0.1/appName/api-auth/login/
# http://127.0.0.1/appName/api-auth/logout/
```

### Authentication

+ `Authentication` is the process of `verifying the user` by `checking the credentials`
+ `Authentication`: How are you?
+ `Authorization`: 	What are you allowed to do? (permissions like  Accessing admin dashboard)
+ `Authentication` by itself wont allow or disallow an incoming request

+ There are 3 types of built-in authentication in DRF: 
    1. SessionAuthentication 
    2. TokenAuthentication 
    3. BasicAuthentication (only for testing)
    + JWTAuthentication is a third-part package

+ Methods of Authentication in programing:
    1. Username & Password         	Most common method
    2. Token Authentication	        Use a secret token (like an API key)
    5. Session Authentication	    Stores login info in session cookies
    3. JWT (JSON Web Token)	        Encodes user identity in a signed token
    4. OAuth	                    Auth via third-party like Google, GitHub
    6. Biometric	                Face ID, fingerprint, etc. (on mobile apps)


```py
REST_FRAMEWORK = {                                              # setting.py     # use only one of these classes
    'DEFAULT_AUTHENTICATION_CLASSES': [                         # apply to all view classes globally
        'rest_framework.authentication.BasicAuthentication',    # For testing          
        'rest_framework.authentication.SessionAuthentication',  # For browsable API    
        'rest_framework.authentication.TokenAuthentication',    # For token-based
        'rest_framework_simplejwt.authentication.JWTAuthentication',
    ],
    'DEFAULT_PERMISSION_CLASSES': [                             # after authentication class
        'rest_framework.permissions.IsAuthenticated',           # set permission class to IsAuthenticated
    ],                                                            # if you do not set permission you see nothing
}      
```


#### 1. Basic Authentication

+ It uses Base64-encoded string username/password sent on every request headers.
+ Not secure over HTTP, use only for testing
+ inside Headers:---> `Authorization: Basic <base64 encoded username:password>`
+ we can use postman and browser     
+ in browser it opens a popup
+ we get 401 error if we do not send headers
+ we should set a permissions_class per view or set a permission globally in settings.py
+ apply to all views with `permission_classes = [permissions.IsAuthenticated]`


#### 2. Session Authentication

+ `SessionAuthentication` uses Django’s built-in session framework to identify users
+ This is useful for users who are logged in via the web interface (like Django Admin [/admin] or a login view)
+ works with Browsable API
+ Enforces CSRF validation for security
+ Uses Django’s session & login views
+ Not for mobile/API clients
+ Stores login info in session cookies
+ + we should set a permissions_class per view or set a permission globally in settings.py
+ apply to all views with `permission_classes = [permissions.IsAuthenticated]`

```py
from django.contrib.auth import authenticate, login, logout

class LoginView(APIView):

    permission_classes = [permissions.AllowAny]             # Allow unrestricted access

    def post(self, request):
        username = request.data.get('username')
        password = request.data.get('password')
        user = authenticate(request, username=username, password=password)
        if user:
            login(request, user)  # Creates a session
            return Response({"status": "Logged in"})
        return Response({"error": "Invalid credentials"}, status=400)

class LogoutView(APIView):
    def post(self, request):
        logout(request)  # Clears the session
        return Response({"status": "Logged out"})

class ProfileView(APIView):
    def get(self, request):
        user = request.user  # Available via session
        return Response({"username": user.username})
```


### 3. Token Authentication

+ stateless authentication which No sessions or cookies needed
+ each user is assigned a unique token.
+ This token must be sent with every request ( in Authorization header)
+ Works great for non-browser clients like Mobile & API clients
+ unlike sessions, a table will create in admin panel for Tokens
+ globally apply for `permission_classes = [permissions.IsAuthenticated]`
+ after adding to INSTALLED_APPS run: `python manage.py migrate`

```py
# 1. set authentication
REST_FRAMEWORK = {                                             # setting.py   
    'DEFAULT_AUTHENTICATION_CLASSES': [                        # apply to all view classes
        'rest_framework.authentication.TokenAuthentication'    # globally apply for permission_classes = [permissions.IsAuthenticated]
    ]                                                          # Token Authentication uses token db system
}  
# 2. installed apps
INSTALLED_APPS = [                                             # base requirement for django rest
    'rest_framework',                                          # no need for userapp, just authtoken
    'rest_framework.authtoken',                                # token based authentication      
]                                                              
# 3. manage.py migrate                                          # bcz it creates a new table that is going to store tokens
```

+ there is 2 option for creating tokens:
    1. Model Token   (we use this for registering since we dont want to transfer user to login page after registering)
    2. Automatically via Login View   

```py
# 1. Model Token
from django.contrib.auth.models import User
from rest_framework.authtoken.models import Token

user = User.objects.get(username='youruser')
token, created = Token.objects.get_or_create(user=user)
print(token.key)

# 2. login view
urlpatterns = [
    path('login/', obtain_auth_token),              # send a post method
]
```


###### a. Login

+ First create a new app (userapp) with this Structure: `python manage.py `

```
userapp\
|---api\
|   |---views.py
|   |---urls.py
|   |---serializers.py
```

+ `obtain_auth_token` is not a browsable page like a login page 
+ `obtain_auth_token` creates a link for sending form-data
+ with POST method in the body send a form-data with (username + password) and in response it gives us a token
+ for every request we use this token inside Headers:---> `Authorization: Token <token>`

```py
from rest_framework.authtoken.views import obtain_auth_token   
urlpatterns = [                                                
    path('account/',include('userapp.api.urls')),              # middle url
    path('login/',obtain_auth_token, name= 'login'),           # send formdata to this url [username,password] and gives us token as response
    path('register/', RegisterView.as_view()),                 # custome registerView
    path('logout/', logout_view),
]
```

##### b. Logout

```py
@api_view(['POST'])
def logout_view(request):
    if request.method == 'POST':                         # delete token in db
        request.user.auth_token.delete()                 # req.user means current user
        return Response(status=status.HTTP_200_OK)
```




###### b. Registeration


+ the process of creating Registeration:
    1. create a serializer
    2. View
    3. URL
    4. create a signal (optional and not important)

+  use form-date in post method

```py
# 1. simple serializer

from django.contrib.auth.models import User                       # User model
class RegisterSerializer(serializers.ModelSerializer):
    password = serializers.CharField(write_only=True)            # write_only ensures its not included in the response

    class Meta:
        model = User
        fields = ['username', 'email', 'password']

    def create(self, validated_data):
        user = User.objects.create_user(**validated_data)         # create a new user
        return user


# 1. serializer with two password field

from django.contrib.auth.models import User                       # User model

class RegistrationSerializer(serializers.ModelSerializer):        # goal: creating two password field for registration 
    password2 =serializers.CharField( write_only = True)          # write_only ensures its not included in the response

    class Meta:                                                   # user in django model has 3 fields by deault when we create them
        model = User                                              # you can write validation for email but by default it is optioanl
        field = ['username', 'email', 'password', 'password2']    # map these fields for recive the post request and respone to it
        extra_kwargs = {                                          # use form-date in post method
            'password': {'write_only': True}                      # write_only has 'Required= True' inside it and not included in response
        }    # Alternatively we can specify password field explicitly above on the serializer class like password2

    def save(self):                                               # save() method is for creating a new user 
        password = self.validated_data['password']                 # without overriding save method we get error 
        password2 = self.validated_data['password2']              # password2 is not in the model and django can not save it automatically

        if password != password2:
            raise serializers.ValidationError({'password': 'Passwords must match'})   # pass a dict

        if User.objects.filter(email = self.validated_data['email']).exists():
            raise serializers.ValidationError({'email': 'Email already exists'})
        
        account = User(                                             # create an instance of User    
            email = self.validated_data['email'],                   # another syntax: User.objects.create()
            username = self.validated_data['username']              # do not return password and password2
        )
        account.set_password(password)                              # for hashing
        account.save()
        return account                                              # return when you call serializer.save() for post request

# 2. view
from rest_framework.authtoken.models import Token         # first create a token model in db by "python manage.py migrate"
from user_app import models                               # import Token  for 'create_auth_token' 

@permission_classes([permissions.AllowAny])
@api_view(['POST'])
def register_view(request):                                           # usual view
    if request.method == 'POST':
        serializer = RegistrationSerializer(data=request.data)
        if serializer.is_valid():
            account= serializer.save()                              # returns from serializer save() method
            token, created = Token.objects.get_or_create(user=account)  
            data = {                                                # customise the response and send token after registration
                'username': account.username,
                'email': account.email,
                'token': Token.objects.get(user=account).key,    # access key of {'token': 7e4s...}
                'message': 'Registration Successful'             # in register serializer we do not create a token
            }                                                   # Token() calls 'def create_auth_token()' for creating a token inline
            return Response(data)                                # inside models we should define 'def create_auth_token()'
        else:
            return Response(serializer.errors)

# 3. signals  (optional and not important)
# if you want to create an inline token with ('token': Token.objects.get(user=account).key) read this section
# by this section we can use 'get()' instead of get_or_create() for creating tokens in RegisterView
# this piece of code is for auto creating tokens when user is created   (create_auth_token)
# place this snippet code in views or in signals.py and then import signals.py in the views.py
# copy this snippet code from Doc
from django.conf import settings        
from django.db.models.signals import post_save                            # generating tokens by using signals
from django.dispatch import receiver
from rest_framework.authtoken.models import Token

@reciever(post_save, sender=setting.AUTH_USER_MODEL)                       # listan and catch the User's post_save signal
def create_auth_token(sender, instance=None, created=False, **kwargs):     # def create_auth_token()
    if created:                                                            # when a user is created, a token will create also
        Token.objects.create(user=instance)                                
```

### 4. JWT Authentication

+ simplejwt package: `pip install djangorestframework-simplejwt`
+ 
+ JWT is a digitally signed token that proves a user's identity. It’s like a digital passport.
+ a JWT is look like this: `xxxxx.yyyyy.zzzzz`
+ it has 3 parts (all Base64 encoded):
    1. Header – type of token and signing algorithm
    2. Payload – user data (claims)
    3. Signature – verifies that the token is untampered

+ JWT is not depondent on databse like 2. Token Authentication
+ The only disadvantage of JWT is that we can not control the tokens from server and db.
+ JWT does not need logout, we just need to delete the token from local storage in frontend.
+ JWTs are not encrypted by default, only signed → don’t store passwords!
+ Always use HTTPS to protect tokens.
+ JWT runs before permissions and throttling


```py
REST_FRAMEWORK = {                                       ### settings.py
    'DEFAULT_AUTHENTICATION_CLASSES': [
        'rest_framework_simplejwt.authentication.JWTAuthentication',
    ]        
}
SIMPLE_JWT = {                                            # override default settings for jwt
    'ACCESS_TOKEN_LIFETIME': timedelta(minutes=30),        # change default
    'REFRESH_TOKEN_LIFETIME': timedelta(days=1),          # change default
    # normally we use just 2 above
    'ROTATE_REFRESH_TOKENS': True,                        # every time we refresh token it will gives us a new token for both
    'BLACKLIST_AFTER_ROTATION': True                      # Invalidates old ones
    'REFRESH_TOKEN_COOKIE_NAME': 'refresh_token',         # change default name
    'REFRESH_TOKEN_COOKIE_HTTP_ONLY': True,               # Inaccessible to JavaScript
    'REFRESH_TOKEN_COOKIE_SAMESITE': 'Strict'              # ?
    'ALGORITHM': 'HS256',                                 # we can change algorithm
}
```

+ there is 2 option for creating JWT tokens:
    1. Model RefreshToken:   (we use this for registering since we dont want to transfer user to login page after registering)
    2. JWT urls:  Automatically via Login View   

##### 1. JWT urls

+ How it works?
    1. Login: User sends a POST request with username & password in the body as form-data to "/api/token/"
    2. Server responds with JWT containing their identity (a pair of tokens: 1. Access Token, 2. Refresh Token)
    3. Client stores them in localStorage
    4. Client sends Access Token in headers for each API call: --->  `Authorization: Bearer <access_token>`
    5. Server verifies the signature and reads the payload If valid → grant access
    6. Token Expiration: When the Access Token expires, client gets Response: 401 Unauthorized
    7. client should use the Refresh Token to request a new Access Token 
    8. in frontend we should detect 401 error and attempt to refresh the Access Token automatically and finally retry the original request
    9. backend checks if Refresh Token is still valid or not
    10. If Refresh Token also expires, user must fully re-authenticate and fronend should redirect to login page

+ Access Token vs Refresh Token:
    + `Access tokens` are short-lived and used to access protected APIs
    + `Refresh tokens` are long-lived and used to obtain new `Access tokens` when the current ones expire.
    1. use this formula inside headeras for protected APIs: `Authorization: Bearer ${access token}` 
    2. send a POST request to /api/token/refresh and write `refresh = ${refresh_token}` inside body with [x-www-form-urlencoded] option
    3. do not send Refresh Token in the header, use it only in the body



```py
from rest_framework_simplejwt.views import TokenObtainPairView, TokenRefreshView    # Doc      # add this to root urls
urlpatterns = [   
    path('api/token/', TokenObtainPairView.as_view(), name='token_obtain_pair'),     # Login (returns both tokens)
    path('api/token/refresh/', TokenRefreshView.as_view(), name='token_refresh'),    # Get new access token
]  
# Response for both:
# {
#     "access": "eyJ...",
#     "refresh": "eyJ..."
# }
```

##### 2. JWT views (RefreshToken)

```py
### views.py
from rest_framework_simplejwt.tokens import RefreshToken

@api_view(['POST'])
def register_view(request):
    if request.method == 'POST':
        serializer = RegistrationSerializer(data=request.data)
        if serializer.is_valid():
            account= serializer.save()    # return from serializer save method
            data = {
                'username': account.username,
                'email': account.email,
                'token_refresh': str(RefreshToken.for_user(account)),        # creating token manually
                'token_access': str(RefreshToken.for_user(account).access_token),
                'message': 'Registration Successful'
            }
            return Response(data) 
        else:
            return Response(serializer.errors)
```


### Throttling

+ Throttling are used to limit the number of requests to a resource
+ Throttling controls how frequently clients can make API requests
+ we can apply throttling a. globally or b. per view class
+ Throttling types:
    1. AnonRateThrottle
    2. UserRateThrottle
    3. custome

+ we can customise throttling in 2 ways:
    1. User Rate Throttling
    2. Scoped Rate Throttling

##### a. Throttling globally
```py
REST_FRAMEWORK = {                                       # DEFAULT_THROTTLE_CLASSES will apply to all view classes globally 
    'DEFAULT_THROTTLE_CLASSES': [                        # There are two types of throttling: 
        'rest_framework.throttling.AnonRateThrottle',    # 1. annonymous(AnonRateThrottle) 
        'rest_framework.throttling.UserRateThrottle'     # 2. per user(UserRateThrottle)
    ],  
    'DEFAULT_THROTTLE_RATES': {                          # default rates
        'anon': '5/day',                                 # an anon user can send 5 request per day
        'user': '10/hour'                                # scope user + scope anon
        'custom': '10/minute'                            # Custom scope (introduce all your cutome throttling)
    }                                                     # after this 5 request per day we get "429 error" (Too Many Request)
}                                                          
```
##### b. Throttling per view class
```py
from rest_framework.throttling import AnonRateThrottle, UserRateThrottle

# 1. CBV
class BookList(generics.ListAPIView):     
    queryset = Book.objects.all()
    serializer_class = BookSerializer
    permission_classes = [permissions.IsAuthenticatedOrReadOnly]
    throttle_classes = [UserRateThrottle, AnonRateThrottle]

# 2. FBV
@api_view(['GET'])
@permission_classes([permissions.IsAuthenticatedOrReadOnly])
@throttle_classes([AnonRateThrottle])
def BookList(request):         # function base view
    return Response("res")
```

##### custom: 1. UserRateThrottle

+  addition to user + anon we want to create a new scope

|api\
|---throttling.py

```py
# 1. settings.py
'DEFAULT_THROTTLE_RATES': {                 # inside settings.py 
    'book-list': '5/day'                # introduce your custome scope
}

# 2. throttling.py
class BookListThrottle(UserRateThrottle):      # custom throttle class
    scope = 'book-list'                        # use your new scope 

# 3. view
class BookList(generics.ListAPIView):                # class base view
    queryset = Book.objects.all()
    serializer_class = BookSerializer
    throttle_classes = [BookListThrottle]       # name of your custome throttle
```

##### custome: 2. ScopedRateThrottle

+ with scopedRate we can directly import witout creating any custome throttling class

```py
# 1. settings.py
'DEFAULT_THROTTLE_RATES': {                 # inside settings.py 
    'book-list': '5/day'                # introduce your custome scope
}

# 2. view
from rest_framework.throttling import ScopedRateThrottle      # another kind of custome throttling
class BookList(generics.ListAPIView):                        # class base view
    queryset = Book.objects.all()          
    serializer_class = BookSerializer                        # just intoduce book-list in settings.py
    throttle_classes = [ScopedRateThrottle]      
    throttle_scope = 'book-list'                             # 'book-list' comes from settings.py
```


### Filtering 

###### 1. Filtering (django-core)

+ Like `where` condition in sql
+ 
+ We have 3 ways for filtering: 
    1. filtering agiainst current user ---> `self.current.user`
    2. filtering against url         -----> `self.kwargs["username"]`
    3. filtering against query parameters ---> `self.request.query_params["username"]`

```py
class BookList(generics.ListAPIView):                 # instead of pk or id we send something else for filtering like a string
    serializer_class = BookSerializer                  # easier way to impleament filtering is to use generics for overrideing get_queryset()
                                                         # we are overriding get_queryset instead of using "queryset = Book.objects.all()"

    # 1. filtering against the url (directly maping the value)
    def get_queryset(self):                            
        firstname = self.kwargs.get('fullname')                  # django url: api/<str:fullname>/
        return Book.objects.filter(author__fullname='pk')        # __fullname for accessing fields of foreign key

    # 2. filtering against query params 
    def get_queryset(self):                           
        fullname = self.request.query_params.get('fullname', None)    # browser url: api/books/?firstname=ali [use & for multiple]
        return Book.objects.filter(author__fullname='pk')            # django url: /api/books/     ---> there is no need for mapping parameters
```

##### 2. Django-filter

+ Django-filter is a powerful third-party package  used to filter querysets `based on URL parameters`
+ django-filter parses the query parameters and applies them automatically
+ instead of writing custom filter logic in your views you can let users send them via query parameters:
+ instead of `Book.objects.filter(author=author_name, year__gte=2020)` send `api/books/?author=ali&year__gte=2020`
+ `pip install django-filter` --> use this package for generic filtering
+ we can only use this approach for `generic views`

```py
INSTALLED_APPS = [                # settings.py
    'django_filters',           # what ever you install should be in INSTALLED_APPS
    'rest_framework',
]
REST_FRAMEWORK = {
    'DEFAULT_FILTER_BACKENDS': ['django_filters.rest_framework.DjangoFilterBackend']
}
```

```py
from django_filters.rest_framework import DjangoFilterBackend

# example 1                                              
class BookList(generics.ListAPIView):                   # we should send parameters in the url like /books/?year=2021&title=Python
    queryset = Book.objects.all()                      # but no need for extracting them in get_queryset()
    serializer_class = BookSerializer                  
    filter_backends = [filters.DjangoFilterBackend]    # Enables django-filter integration (exact matching for keywords)
    filterset_fields = ['year', 'title']                 # we can send both or one of them inside url parameters
    # /books/?year=2021                                   # All books from 2021  (exact metach of 2021)
    # /books/?title=Python                               # All books with exact title "Python"
    # /books/?year=2021&title=Python                    # 2021 books with title "Python"

# example 2  (related fields)
class BookList(generics.ListAPIView):                                  # /books/?year=2021&author__fullname=ali
    queryset = Book.objects.all()                                       # /books/?year=2021&author__age=30
    serializer_class = BookSerializer                                    # /books/?author__age=30
    filter_backends = [filters.DjangoFilterBackend]
    filterset_fields = ['year', 'author__fullname', 'author__age']      # use __ for related fieldsg

# example 3
class BookList(generics.ListAPIView):                # /books/?search=war
    queryset = Book.objects.all()                     # /books/?search=man
    serializer_class = BookSerializer                 # DjangoFilterBackend is for 'exact matching' and SearchFilter is for 'include matching'
    filter_backends = [filters.SearchFilter]          # use for 'include matching' of keywords like 'man' inside 'superman'
    search_fields = ['author__fullname', 'title']    # we can use regex pattern for search_fields like: '^title$'
    search_fields = ['=author__fullname', '^title']    # = is for exact matching   and ^ means start with

# example 4
class BookList(generics.ListAPIView):                # /books/?ordering=rating
    queryset = Book.objects.all()                     # /books/?ordering=-rating
    serializer_class = BookSerializer                 # /books/?ordering=rating,age
    filter_backends = [filters.OrderingFilter]        # use for sorting 
    ordering_fields = ['rating', 'age']               # -rating for decending and =rating,age for multiple ordering
    ordering_fields = '__all__'                       # allow all model fields to be orderable

# example 5
class BookList(generics.ListAPIView):                                 # 
    queryset = Book.objects.all()
    serializer_class = BookSerializer
    filter_backends = [filters.SearchFilter, filters.OrderingFilter]  # multiple filter
    search_fields = ['author__fullname', 'title']                     # Enable ?search=...
    ordering_fields = ['rating']                                      # Enable ?ordering=rating or ?ordering=-rating
```

##### 2. Django-filter (custome FilterSet)

+ in custome FilterSet we can use lookups
+ lookup expersions like: 'exact', 'iexact', 'contains', 'icontains' , 'gt', 'gte', 'in' and so on

```py
# 1. filters.py
class BookFilter(django_filters.FilterSet):                                         # 
    min_year = django_filters.NumberFilter(field_name='year', lookup_expr='gte')
    max_year = django_filters.NumberFilter(field_name='year', lookup_expr='lte')
    title_contains = django_filters.CharFilter(field_name='title', lookup_expr='icontains')

    class Meta:
        model = Book
        fields = ['author', 'min_year', 'max_year', 'title_contains']

# 2. views.py
class BookList(generics.ListAPIView):                      # /books/?author=3
    queryset = Book.objects.all()                          # /books/?min_year=2020
    serializer_class = BookSerializer                      # /books/?max_year=2021
    filter_backends = [filters.DjangoFilterBackend]        # /books/?min_year=2000&max_year=2010
    filterset_class = BookFilter                           # class name
```

### pagination

+ we can set pagination globally or per view
+ we have 3 types of pagination:
    1. page number
    2. limit offset
    3. cursor

+ in DRF pagination works with url params
+ each req gives us a json like: {count:57, next: url/api/?limit=10&offset=20, resutls=[data] }

##### 1. global pagination
```py
REST_FRAMEWORK ={   # in globall pagination you should change your views if using FBV or class APIView, but no for other views
    'DEFAULT_PAGINATION_CLASS': 'rest_framework.pagination.LimitOffsetPagination',   
    'DEFAULT_PAGINATION_CLASS': 'rest_framework.pagination.PageNumberPagination',
    'DEFAULT_PAGINATION_CLASS': 'rest_framework.pagination.CursorPagination',
    'DEFAULT_PAGINATION_CLASS': 'apps.core.pagination.CustomPagination'         # custome
    'PAGE_SIZE': 10             # user can change page_size with url manually despite this number
}    
```

##### 2. per view pagination

```py
from rest_framework.pagination import PageNumberPagination

class BookViewSet(viewsets.ModelViewSet):
    pagination_class = PageNumberPagination
    page_size = 10                                 # Overrides global PAGE_SIZE
```


#### 2. per view pagination (FBV)
```py
from rest_framework.pagination import PageNumberPagination           

@api_view(['GET'])                          # in function base view we need paginator
def book_list(request):                      # we call two function from paginator: 1.paginate_queryset 2.get_paginated_response
    paginator = PageNumberPagination()      # call type of paginator
    paginator.page_size = 10               # Set size of the paginator
    
    queryset = Book.objects.all()             # paginator is our variable
    result_page = paginator.paginate_queryset(queryset, request, context={'request': request})         # paginate_queryset()
    
    serializer = BookSerializer(result_page, many=True)                # pass result instead of queryset
    return paginator.get_paginated_response(serializer.data)           # instead of returning serializer.data return paginator
```

#### Custom Pagination

|api
|---pagination.py


```py
# 1. custom pagination
class CustomPagination(PageNumberPagination):       # create a custome pagination class in pagination.py
    page_size = 5           
    page_size_query_param = 'page_size'         # define outside the class for inheritance
    max_page_size = 100

# 2. views
class BookListView(APIView):
    pagination_class = CustomPagination
    
    def get(self, request):                     # very similar to FBV
        queryset = Book.objects.all()
        paginator = self.pagination_class()     # paginator is a variable
        result_page = paginator.paginate_queryset(queryset, request)
        serializer = BookSerializer(result_page, many=True)
        return paginator.get_paginated_response(serializer.data)
```


######  a. PageNumberPagination (recommend)

+ URL: /books/?page=2        ---->     shows page 2

```py
from rest_framework.pagination import PageNumberPagination       
class BookListPagination(PageNumberPagination):
    page_size = 10                # default size     # first page url: /api/   ---> next url: /api/?page=2
    page_query_param = 'p'      # instead of "page" word in url: /api/?p=2
    page_size_query_param = 'size'     # user can overwrite "page_size" word inside url: /api/?p=3&size=20
    max_page_size = 100            # user can not overwrite page_size with larger values
    last_page_strings = ['end']     # by default we can get the last page by: /api/?page=last

class BookList(generics.ListAPIView):   
    queryset = Book.objects.all()                
    serializer_class = BookSerializer
    pagination_class = BookListPagination
```

##### b. LimitOffsetPagination

+ URL: /books/?limit=10&offset=20   ---->  shows elements from 21 to 30 (Skips 20 items, returns 10)
+ useful when you need random access to different parts of a dataset

```py
from rest_framework.pagination import LimitOffsetPagination  # limit = page_size    offset= number of skip
class BookListPagination(LimitOffsetPagination):  # offset=10 means skip 10 elements and load from 11
    default_limit = 10      # url: /api/?limit=10&offset=2
    max_limit = 100          # max_page_size
    limit_query_param = 'limit'        # alternative word inside url params
    offset_query_param = 'start'        # limit and offset = start and limit

# /books/                      # Gets first 10 items (default_limit)
# /books/?limit=5              # Gets first 5 items
# /books/?offset=10            # Skips first 10 items
# /books/?limit=5&offset=10    # Gets 5 items starting from 11th
```

##### c. CursorPagination

+ we do not have `page number` anymore, we just have `next` and `previous`. 
+ user can not jump to specific page number. we force user to click multiple times on `next`.
+ its depond on ordering and timing (`created` filed inside django models). its neccessary.
+ useage: 1. Infinite scroll interfaces 2. large dataset

```py
from rest_framework.pagination import CursorPagination      # cursor pagination does not work with 'OrderingFilter'
class BookListPagination(CursorPagination):
    page_size = 10               # user can not change this value
    ordering = 'created'        # default is "-created" and if we do not have this field in model we get error
    cursor_query_param = 'record'   # by default: 'cursor'
```


### API Testing
|app\
|---tests\
we have two kind of unsuccessful test: 1.failuer 2. errors (syntax)
##### 1. Login testing

```py
from rest_framework.test import APITestCase       # tests.py inside app folder
from rest_framework import status              #  status 201 means Successful registration
from django.urls import reverse                 # with reverse we access our urls with named_urls
from django.contrib.auth.models import User      # import user from djagno models or your models

class RegisterTestCase(APITestCase):    
    def test_register(self):           # all methods of class should start with 'test'
        data = {
            "username" : "testuser",        # django creates this user temporarily just for this test
            "email" : "w5HdA@example.com",   # create fields for your serializer and model from through the url
            "password" : "testpassword",     
            "password2" : "testpassword"
        }       # with self.client we can send HTTP request bcz it inherits from APITestCase
        response = self.client.post(reverse('register'), data)    # first parameter is url
        self.assertEqual(response.status_code, status.HTTP_201_CREATED)   # what you expected from response?   

class LoginLogoutTestCase(APITestCase):

    def setUp(self):            # initial setup with "setUp" keyword
        self.user = User.objects.create_user(username="testuser", password="testpassword")     # create user for the class 
    # create user using model and since serializer is not responsible here we do not need email and password2
    def test_login(self):
        data = {
            "username" : "testuser",          # use the same data in setUp
            "password" : "testpassword"
        }
        response = self.client.post(reverse('login'),data)            # with this named_url it tests our views
        self.assertEqual(response.status_code, status.HTTP_200_OK)     # the self.client use self.user inside setUp for login

    def test_logout(self):                                     # from rest_framework.authtoken.models import Token
        token = Token.objects.get(user__username="testuser")    # token authentication using database and we get it from models
        self.client.credentials(HTTP_AUTHORIZATION='Token ' + token.key)     # token inside credentials
        response = self.client.post(reverse('logout'))
```

##### 2. Views testing
```py
class BookTestCase(APITestCase):
    def setUp(self):           
        self.user = User.objects.create_user(username="testuser", password="testpassword")   # create a user 
        self.token = Token.objects.get(user__username="testuser")            # get token from model
        self.client.credentials(HTTP_AUTHORIZATION='Token ' + self.token.key)  # pass the token inside credentials
        self.author = Author.objects.create(name="testauthor")            # author is foreign key
        self.book = Book.objects.create(title="testtitle", rating=5, author=self.author)         # create some data to access elements of it

    def test_book_create(self):              # we can test all kinds of views with defining methods inside one class
        data = {                             # excepet viewsets we should use exact named_url inside urls.py
            "title" : "testtitle",
            "rating" : 5,
            "author" : self.author.id
        }                      
        response = self.client.post(reverse('book-list'),data)    # for viewsets we can add "-list" and "-detail" to the end of named_url
        self.assertEqual(response.status_code, status.HTTP_201_CREATED)  # viewsets have one named_url for both list and detail
    
    def test_book_list(self):                                   # book-list and book-detail are named_urls
        response = self.client.get(reverse('book-list'))         # just send a get request witout any data

    def test_book_individual_element(self):               # first we should create some data using self
        response = self.client.get(reverse('book-detail',args=[self.book.id]))   # second argument of reverse is id of created data in setUp
        self.assertEqual(response.status_code, status.HTTP_200_OK)    # reverse(named_url, <pk>) url:/book/<pk> and we get pk with args in reverse
        self.assertEqual(response.data['title'], "testtitle")
        self.assertEqual(models.Book.objects.get(id=self.book.id).title, "testtitle")
        self.assertEqual(models.Book.objects.count(), 23)             # test if count is 23

    def test_book_unauthorized(self):  # we can also create a method foe unauthorized user
        self.client.force_authenticate(user=None)    # now we are not logged in
        response = self.client.get(reverse('book-list'))
        self.assertEqual(response.status_code, status.HTTP_401_UNAUTHORIZED)

    def test_book_update(self):             # for update and delete we should create a data manually inside setUp
        response = self.client.put(reverse('book-detail',args=[self.book.id]),data={})   # put for update

```


### settings.py


```py
from pathlib import Path
from datetime import timedelta
import os

# 1. env
from dotenv import load_dotenv         # create a .env file for passwords, ports, urls and secret keys
load_dotenv()                         #  inject envirenmont variables into settings

# 2. allowd hosts                     # list of host/domain names that Django will serve requests for them
ALLOWED_HOSTS = []                   # for local development only # In production, leaving it empty will break your app
ALLOWED_HOSTS = ["*"]                 # not recommended
ALLOWED_HOSTS = ["127.23.0.12", "api.example.com", "localhost"]    # ip of frontend

# 3. cors                           # pip insall django-cors-headers
INSTALLED_APPS = ["corsheaders"]
MIDDLEWARE = ["corsheaders.middleware.CorsMiddleware"]
CORS_ALLOWED_ORIGINS =    True
CORS_ALLOWED_CREDENTIALS = True

# 4. db
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.postgresql',
        'NAME': 'postgres',                  # os.getenv("DB_NAME")   is better to get from env file
        'USER': 'postgres',
        'PASSWORD': '123',
        'HOST': '127.23.0.2',
        'PORT': '5432'
    }
}
```






