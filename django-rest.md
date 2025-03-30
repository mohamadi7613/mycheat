

# django rest


# Terminal
```bash
pip show djangorestframework           # be sure you do not using venv or poetry or ...
```

# Installation
```py
INSTALLED_APPS = [       
    'rest_framework',        # base requirement for django rest
]
```
# Structure
```py
app\
|---api\
|   |---views.py
|   |---urls.py
|   |---serializers.py
|   |---permissions.py         # custome permissions
```

### Browsabel API
```py
REST_FRAMEWORK = {                             # how to change the browsable api page in our browser? 
    'DEFAULT_RENDERER_CLASSES': (               # gobally change
        'rest_framework.renderers.JSONRenderer',          # pure json insted of browsable API
    )
}
class BookList(generics.ListAPIView):                # for specific view   (not recommended)
    render_classes= [renderers.JSONRenderer]     
```

### function base view
```py
from rest_framework.response import Response
from rest_framework.decorators import api_view        # error: .accepted_renderer not set on Response
from rest_framework import status

@api_view(['GET','PUT','DELETE'])            # decorator for function base views   # PUT = update all fileds, PATCH = update one or some fields
def book_list(request,pk):                   # function base view without decorator gives 'AssertionError' for .accepted_renderer
    if request.method == 'GET':           # @api_view handles unsupported methods with a `405 Method Not Allowed` response.
        try:                                           # if any books does not find we get error
            books = Book.objects.all()                   # Get a queryset of books in the form of model instance (complex data)
            book_list = Book.objects.values()            # Get qyueryset of books in the form of dictionary
            serializer = BookSerializer(books, many=True)   # Serialize the queryset to object  # use many when we have more than one data
            return Response(serializer.data)          # alternative way for serializer: we can use JsonResponse in django-core
        except ObjectDoesNotExist:              # if you dont use 'many=True' --> Got AttributeError when attempting to get a value for field 
            return Response({"error": "Books do not exist"}, status=404)

    if request.method == 'PUT':                       # PUT is for update all fields
        book = Book.objects.get(pk=pk)                # Get pk from url
        serializer = BookSerializer(book, data=request.data)  # Deserialize the data with (old data, new data) or (queryset, data)
        if serializer.is_valid():
            myvar = serializer.validated_data['active']       # we can access validated data using dictionary
            serializer.save()
            return Response(serializer.data)    # status code is 200 by default
        else:
            return Response(serializer.errors, status.HTTP_400_BAD_REQUEST)

    if request.method == 'DELETE':
        book = Book.objects.get(pk=pk)                # Get the book for deleting
        book.delete()                                # call delete from model
        return Response(status=status.HTTP_204_NO_CONTENT)    # 204 is for succuesfully no content
```
## Class base view (1. APIView)
```py
from rest_framework.views import APIView
class BookList(APIView):                 # class base view inherit from APIView [we dont need @api_view decorators like function based]

    def get(self, request):                         # get method for http get requests
        books = Book.objects.all()                  # get all querysets
        serilizer = BookSerializer(books, many=True)
        return Response(serializer.data)

    def post(self, request, pk):                        # POST method is for creating a new data
        serilizer = BookSerializer(data=request.data)    # Modelserializer (not regular serializer) is equal for both get and post 
        if serializer.is_valid():                      # if we want to create an object with serializer we should use 'data=' as an argument
            serializer.save()
            return Response(serilizer.data)            # status is 200 by default
        else:
            return Response(serializer.errors)
```
### Class base view (2. using mixins)
The mixin classes provide specific view behavior by action methods( not handler methods like get(), post(), etc.)
```py
from rest_framework import mixins, generics
# GenericAPIView extends APIView, adding commonly required behavior for list and detial views.
class BookList(mixins.ListModelMixin, mixins.CreateModelMixin, generics.GenericAPIView):
    # for using mixins we need to inherit from GenericAPIView in the last
    queryset = Book.objects.all()                    # queryset is a keyword for the class otherwise we should override the `get_queryset()` method
    serializer_class = BookSerializer                # serializer_class is a keyword for the class otherwise override the `get_serialiser()` method
    # there is no need for passing something to serializer
    def get(self, request, *args, **kwargs):          
        return self.list(request, *args, **kwargs)   # list() acion provided by ListModelMixin
    def post(self, request, *args, **kwargs):         # in browsable API creates 'HTML form' and 'Raw data'
        return self.create(request, *args, **kwargs)  # .retrive(), .update(), .destroy()


class BookDetail(mixins.RetrieveModelMixin, generics.GenericAPIView):
    queryset = Book.objects.all()           # pk should send in http params but there is no need to recive pk from url
    serializer_class = BookSerializer
    def get(self, request, *args, **kwargs):
        pk = kwargs.get('pk')                       # there is no need for doing this
        return self.retrieve(request, *args, **kwargs)
```

### Class base view (3. using generics)
generic views for creating, reading and so on: 1. ListAPIView 2. CreateAPIView 3. RetrieveAPIView 4. UpdateAPIView 5. DestroyAPIView
```py
from rest_framework import generics         # Generic views reduce the amount of code needed to write. (concrete views)
class BookList(generics.ListCreateAPIView):    
# ListCreateAPIView already have 'def get(self, req, *args, **kwargs)' section inside its implementation
    queryset = Book.objects.all()               # there is no need for get() method
    serializer_class = BookSerializer           # there is no need for Response and HTTP_Code

class BookDetail(generics.RetrieveUpdateDestroyAPIView):  # mixed one  # get(), update(), delete()
    queryset = Book.objects.all()         # just send an id in http params
    serializer_class = BookSerializer

class BookList(generics.ListCreateAPIView):    
    serializer_class = BookSerializer
    def get_queryset(self):        # instead of 'queryset' we can override it for customazation
        pk = self.kwargs.get('pk')   # queryset handles extracting pk from url params by default
        return Book.objects.filter(author=pk)

    def perform_create(self, serializer):  # overwrite # perform_update(), perform_destroy()
       pk = self.kwargs.get('pk')           # no need for request.data
       author = Author.objects.get(pk=pk)   # in seriliazer we need to exclude author bcz we get if from url (no need for mapping)
       serializer.save(author=author)      # pk is inside url not in request.data
```

### Class base view (4. using viewsets)
viewsets does not porvide `.get()` and `.post()` handlers, and instead porvides `.list()` and `.retrieve()` actions.
```py
from rest_framework import viewsets        # inside one class we can handle everything
class BookViewSet(viewsets.ModelViewSet):  # for list and detail we create one class
    def list(self, request):                  # when we need to get list as well as get detail of specific item its better to combine them
        books = Book.objects.all()             # we can combine 'list' and 'detail' by using viewset 
        serializer = BookSerializer(books, many=True)
        return Response(serializer.data)

    def retrieve(self, request, pk):
        book = Book.objects.get(pk=pk)
        serializer = BookSerializer(book)
        return Response(serializer.data)

    def create(self, request):          # for post method
        serializer = BookSerializer(data=request.data)
        if serializer.is_valid():
            serializer.save()
            return Response(serializer.data)
    def update(self, request, pk):      # for put method
    def destroy(self, request, pk):     # for delete method
# after creating viewset we need to register it in routers in urlpatterns bcz it has both list and retrieve
from rest_framework import routers               # routers help us to combine all type of link
router = routers.DefaultRouter()                 # with routers we can write one url for both list and detail
router.register('books', BookViewSet.as_view(), basename='book')   # /books + /books/12
urlpatterns = [                               # its better to not use custome urls with viewsets
    path('', include(router.urls))     # empty string
]
```

### Class base view (5. using ModelViewSet)
```py
class BookViewSet(viewsets.ModelViewSet):         # ModelViewSet provides all methods 
    queryset = Book.objects.all()               # list(), retrieve(), create(), update(), destroy()
    serializer_class = BookSerializer

class BookViewSet(viewsets.ReadOnlyModelViewSet):         # ReadOnlyModelViewSet provides only list() and retrieve()
```


## Serialization
we have 2 types of serialization: 1. Regular serializer 2. ModelSerializer

#### 1. Regular serializer
**Serialization**: process of converting complex data types (such as Django models or querysets) into a format that can be easily rendered into JSON
**Deserialization**: process of converting JSON into a Python object
In django-core for serialization we have 3 steps: 1. get a queryset 2. convert queryset to object or list 3. convert object to json using JsonResponse
```py
# serializer.py
from rest_framework import serializers                 # with regular serializer we should map everything manually

class BookSerializer(serializers.Serializer):          # serializer is a class inherited from method 1. regular Serializer
    id = serializers.IntegerField(read_only=True)       # read_only means it can not be updated
    name = serializers.CharField(max_length=100)        # we can use validators [serializer level validations]
    active = serializers.BooleanField()                 # these property names should be same as in model
    # if we dont mention a field of model in serializer, that field wont map and we can not see that in the output
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
```

### Serializer Validations
we have 3 types of validations: 1. Filed level validations 2. Object level validations 3. Serializer level validations
```py
from django.core.validators import MaxValueValidator, MinValueValidator   # value fo int (not length for string)
# validatros is equal for both ModelSerializer and  regular serializer
class BookSerializer(serializers.Serializer):          # Each 'Serializer Filed' has 'core arguments' like read_only, write_only, required, ...
    id = serializers.IntegerField(read_only=True)       # serializer level validations  
    name = serializers.CharField(max_length=100)        # serializer level validations
    my_custome_filed = serializers.CharField(default="me", read_only= True)   # custome field can not be existed in our model and db
    description = serializers.CharField(validators=[myfunction])    # validator function (serializer level validations)
    rating = sesrializers.PositiveIntegerField(validators= [MinValueValidator(1), MaxValueValidator(5)])   # serializer level validations
    # in this exmaple name is required bcz it  does not have a default value, so if our request does not contain it we get error    
    def validate_name(self, value):                      # Field level validations for specific property (validate_FILEDNAME)
        if len(value) < 3:     # value is the value of name attribute
            raise serializers.ValidationError("Name is too short")  # return a json {name: "Name is too short"}
        else:                                         # this validations only work for post or put requests (not get request)
            return value
    
    def validate(self, data):                            # Object level validations [we access all properties]
        if data['name'] == data['active']:              # data is obj
            raise serializers.ValidationError("Name can not be same as active")   # send a json {non_field_errors: "Name can not ..."}
        else:
            return data

def myfunction(value):   # function outside the class
    if len(value) < 10:   # no need for return in validatros function
        raise serializers.ValidationError("Description is too short")   # raise not return
```
#### 2. ModelSerializer
```py
class BookSerializer(serializers.ModelSerializer):   # there is no need to create() and update() anymore

    full_name = serializers.SerializerMethodField(read_only=True)    # custom field 
    first_name = serializers.CharField(mex_length=50)   # we can override fields if we want in ModelSerializer like regular serializer
    # if you dont use read_only for custome fields it gets error for post request bcz this field is not in db
    class Meta:                         # with ModelSerializer default Fields with default validatros will be mapped automatically
        model = Book                     # in regualt serializer we do not introduce the model
        fields = '__all__'               # all fields will be serialized
        fields = ['first_name', 'active']     # only these fields will be serialized 
        exclude = ['rating']             # these fields will not be serialized [we can not use 'exclude' with 'fields' together]         
    
    def get_full_name(self, obj):                      # SerializerMethodField should start with ['get_' + PROPERTYNAME ]
        return obj.first_name + " " + obj.last_name      # we access all properties with 'obj'

    def validate_first_name(self, value):                      # Filed level validations like Regular Serializer
        pass                                                # ['validate' + PROPERTYNAME]

    def validate(self, data):                            # Object level validations like Regular Serializer
        pass
```
#### Serializer relations
```py
class AuthorSerializer(serializers.ModelSerializer):  # nested serializer 
    # inside Book model: author = models.ForeignKey(Author, on_delete=models.CASCADE, related_name='books')
    books = BookSerializer(many=True, read_only=True)    # books is related_name inside Book model   # return whole content of obj
    books = serializer.StringRelatedField(many=True)              # __str__ from Book model
    books = serializer.PrimaryKeyRelatedFiled(many=True, read_only=True)           # id from Book model obj
    books = serilaizer.HyperLinkRelatedFiled(many=True,read_only=True, view_name='from_urlpatterns')   # link  to Book model object(forien key)
    # read_only means we can not update it with post method
    class Meta:                       # (many= True) means return an array of objects
        model = Author               # one book have one author but author can have many books
        fields = '__all__'           # books is not a model field for author
        depth = 1                     # shows nested object instead of id  [makes trouble for post request]

class BookSerializer(serializers.ModelSerializer):   # nested serilaizer
    author_obj = serializer.SerializerMethodField()  #  custom field
    class Meta:     # inside Book model we have a foreign key and besides thad id we want to show the whole object
        model = Book           # insted of 'get_author_obj' we can just use 'depth = 1'
        fields = '__all__'

    def get_author_obj(self, obj):                      # get_   
        return AuthorSerializer(obj.author).data        # return whole content of obj by passing the id
```

## 3. HyperLinkedModelSerializer
```py
class BookSerializer(serializer.HyperLinkedModelSerializer):   # very similar to ModelSerializer
    class Meta:             # insted of id, it creates a link 
       model: Book          # we need to add context={'request': request} when we initialise the serializer in View
       fields = '__all__'   # we need to cretae a specific url in urlpatterns with a specific View for hyperlink
```
### User Model
```py
from django.contrib.auth.models import User    # using user model in models as field
class Review(models.Model):                  # get user from /admin page for using in other models
    book = models.ForeignKey(Book, on_delete=models.CASCADE, related_name='reviews')        
    reviewer = models.ForeignKey(User, on_delete=models.CASCADE, related_name='reviews')      # we should change serializer too
    content = models.TextField()

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
with permissions a stranger can not 'access' your data (restriction)
we have two levels: 1. setting level(for all links) 2. object level(for specific links)
django provides 3 types of permissions: 1. IsAuthenticated(log in) 2. IsAdminUser 3. IsAuthenticatedOrReadOnly(only get request for not login users)  4. AllowAny (by default)
#### permissions: 1.setting level
```py
REST_FRAMEWORK = {           # setting.py
    'DEFAULT_PERMISSION_CLASSES': [       # default = globally accessible
        'rest_framework.permissions.IsAuthenticated'    # only accessed by logged in users
    ]
}
```

#### permissions: 2. object level
```py
from rest_framework import permissions
class BookList(generics.ListAPIView):    # class base view
    queryset = Book.objects.all()
    serializer_class = BookSerializer
    permission_classes = [permissions.IsAuthenticatedOrReadOnly]   # only accessed by logged in users otherwise read only for everyone


@permission_classes([permissions.IsAdminUser])       # decorator is needed for function base view
@api_view(['GET'])                      #
def Booklist(request):                  # function base view
   pass 
```
#### custome permissions
|api\
|----permissions\
```py
from rest_framework import permissions       # for customazation we can override two methods
class AdminOrReadOnly(permissions.IsAdminUser):    # my custome permission   # we can inherit fomr BasePermission or so on
    def has_permission(self, request, view):
        base_permission = super().has_permission(request, view)             # check default permission that inherit from parent
        admin_permission = bool(request.user and request.user.is_staff)     # check if the user is admin or not  (is_staff means admin)
        return admin_permission or request.method == 'GET'   # admin or readonly
        if request == 'DELETE':                        # we can customize for specific methods
            return True                             # request.user returns 'AnonymousUser' or username
        if request.user.username = "mohamad":        # check specific users
        if request.user.IsAuthenticated:             # check authentication
        # has_permission runs before the view is called and checks general permissions and only access to the req and view but not object
class ReviewUserOrReadOnly(permissions.BasePermission):      # has_object_permission runs after the view gets the specific object
    def has_object_permission(self, request, view, obj):     # object-level : permissions for a specific 'model' instance
        if request.method in permissions.SAFE_METHODS:          # SAFE_METHODS is GET
            return True                                        #  access only by owner otherwise read only
        else:                                                 # else means: POST, PUT, PATCH, DELETE
            return obj.reviewer == request.user           # check if the reviewer is the same as the user [access object level]
```

### Temporary login
```py
urlpatterns = [   # Authentication 
    path('api-auth/', include('rest_framework.urls'))         # create a temp login for normal users and superusers
]  # adds /appName/api-auth/login/
```
### Authentication
Authentication by itself wont allow or disallow an incoming request.
Authentication is the process of verifying the user by checking if the credentials are correct.
There are 3 types of authentication: 1. SessionAuthentication 2. TokenAuthentication 3. BasicAuthentication(only for testing)
JWTAuthentication is a third-part package which is importnat

#### 1. Basic Authentication
```py
REST_FRAMEWORK = {        # setting.py   # 1. BasicAuthentication uses base64 method
    'DEFAULT_AUTHENTICATION_CLASSES': [          # apply to all view classes
        'rest_framework.authentication.BasicAuthentication'   # globally apply for permission_classes = [permissions.IsAuthenticated]
    ]  # inside Headers:---> Authorization: Basic <base64 encoded username:password> 
}  # we can use postman and browser     # we get 401 error if we do not send headers
```

### 2. Token Authentication
```py
REST_FRAMEWORK = {        # setting.py   2. Token Authentication uses token db system
    'DEFAULT_AUTHENTICATION_CLASSES': [          # apply to all view classes
        'rest_framework.authentication.TokenAuthentication'   # globally apply for permission_classes = [permissions.IsAuthenticated]
    ]  # inside Headers:---> Authorization: Token <token>
}  
INSTALLED_APPS = [
    'rest_framework',               # base requirement for django rest
    'rest_framework.authtoken',     # token based authentication
] # manage.py migrate  ---> bcz it creates a new table that is going to store tokens
```

###### a. Login
First create a new app: userapp
userapp\
|---api\
|   |---views.py
|   |---urls.py
|   |---serializers.py
```py
from rest_framework.authtoken.views import obtain_auth_token   # its a login non-browsable link that we can send our request
urlpatterns = [      # its not a login page (Browsabel api) like temporary login (api-auth/)
    path('account/',include('userapp.api.urls')),            # middle url
    path('login/',obtain_auth_token, name= 'login'),           # send formdata to this url [username,password] and gives us token as response
    # for a pair of (username + password) it gives us a specific token
    # for any request to get data we should add this token in the header
]
```

###### b. Registeration
the process of creating Registeration:
1. Model:       auto create token
2. Serializer
3. View
4. URL


```py
from rest_framework import serializers
from django.contrib.auth.models import User

class RegistrationSerializer(serializers.ModelSerializer):
    password2 =serializers.CharField( write_only = True)   # write_only ensures its not included in the response
    class Meta:                                        # user in django model has 3 fields by deault when we create them
        model = User                                   # you can write validation for email but by default it is optioanl
        field = ['username', 'email', 'password', 'password2']    # map these fields for recive the post request and respone to it
        extra_kwargs = {                                        # use form-date in post method
            'password': {'write_only': True}                  # write_only has 'Required= True' inside it
        }    # Alternatively we can specify password field explicitly on the serializer class like password2
    def save(self):    # without overriding save method we get error bcz of password2 is not in the model and django can not save it
        password = self.validated_data['password']
        password2 = self.validated_data['password2']

        if password != password2:
            raise serializers.ValidationError({'password': 'Passwords must match'})   # pass a dict

        if User.objects.filter(email = self.validated_data['email']).exists():
            raise serializers.ValidationError({'email': 'Email already exists'})
        
        account = User(          # create an instance of User    # another syntax: User.objects.create()
            email = self.validated_data['email'],     # do not return password and password2
            username = self.validated_data['username']
        )
        account.set_password(password)         # for hashing
        account.save()
        return account            # return when you call serializer.save() for post request

### views.py
from rest_framework.authtoken.models import Token        
from user_app import models         # first create a token model and then import  for 'create_auth_token' for placing the snippet code

@api_view(['POST'])
def register_view(request):     # usual view
    if request.method == 'POST':
        serializer = RegistrationSerializer(data=request.data)
        if serializer.is_valid():
            account= serializer.save()    # returns from serializer save() method
            data = {         # we should customise the response bcz we want to send token after registration
                'username': account.username,
                'email': account.email,
                'token': Token.objects.get(user=account).key,    # access key of {'token': 7e4s...}
                'message': 'Registration Successful'
            }
            return Response(data) 
        else:
            return Response(serializer.errors)


### models.py  ===> Token model ---> this is for auto creating tokens for registreation
from django.conf import settings        # copy this snippet code from Doc
from django.db.models.signals import post_save    # generating tokens by using signals
from django.dispatch import receiver
from rest_framework.authtoken.models import Token
@reciever(post_save, sender=setting.AUTH_USER_MODEL)         # catch the User's post_save signal.
def create_auth_token(sender, instance=None, created=False, **kwargs):
    if created:                                   # when a user is created, a token will create also
        Token.objects.create(user=instance)        # place this snippet code in views
```

##### c. Logout
```py
@api_view(['POST'])
def logout_view(request):
    if request.method == 'POST':             # delete token in db
        request.user.auth_token.delete()     # req.user means current user
        return Response(status=status.HTTP_200_OK)
```

### 3. JWT Authentication
`pip install djangorestframework-simplejwt`
JWT is not depondent on databse like 2. Token Authentication
The only disadvantage of JWT is that we can not control the tokens from server and db.
JWT does not need logout, we just need to delete the token from localstorage
```py
### settings.py
REST_FRAMEWORK = {    # setting.py   3. JWT Authentication
    'DEFAULT_AUTHENTICATION_CLASSES': [
        'rest_framework_simplejwt.authentication.JWTAuthentication',
    ]        
}
SIMPLE_JWT = {                   # override default settings for jwt
    'ACCESS_TOKEN_LIFETIME': timedelta(minutes=5),      # change default
    'REFRESH_TOKEN_LIFETIME': timedelta(days=1),         # change default
    'ROTATE_REFRESH_TOKENS': True,                  # every time we refresh token it will gives us a new token for both
    'ALGORITHM': 'HS256',                           # we can change algorithm
}
### urls.py
from rest_framework_simplejwt.views import TokenObtainPairView, TokenRefreshView    # Doc
urlpatterns = [   
    path('api/token/', TokenObtainPairView.as_view(), name='token_obtain_pair'),   # pair: Access Token(short term) + Refresh Token(long term)
    path('api/token/refresh/', TokenRefreshView.as_view(), name='token_refresh'),  # AT: 5 min + RT: 24 hours
]  # post request to /api/token ---> form-data: username, password ----> it sends frontend a pair of token ---> save in localstorage
# use this formula inside headeras for any other apis: `Authorization: Bearer ${access token}` and this token will expire after 5 min
# post request to /api/token/refresh ---> inside body [x-www-form-urlencoded]: `refresh = ${refresh token}` 
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
Throttling are used to limit the number of requests to a resource

##### a. Throttling globally
```py
REST_FRAMEWORK = {     # Setting.py
    'DEFAULT_THROTTLE_CLASSES': [    #There are two types of throttling: 
        'rest_framework.throttling.AnonRateThrottle',           # 1. annonymous(AnonRateThrottle) 
        'rest_framework.throttling.UserRateThrottle'            # 2. per user(UserRateThrottle)
    ],  # this default setting will apply globally to all view classes [commit this one if you want to apply to specific view classes]
    'DEFAULT_THROTTLE_RATES': {   # after this 5 request per day we get "429 error" (Too Many Request)
        'anon': '5/day',        # an anon user can send 5 request per day
        'user': '10/hour'        # scope user + scope anon
    }
}
```
##### b. Throttling per view
```py
from rest_framework.throttling import AnonRateThrottle, UserRateThrottle
class BookList(generics.ListAPIView):     # class base view
    queryset = Book.objects.all()
    serializer_class = BookSerializer
    permission_classes = [permissions.IsAuthenticatedOrReadOnly]
    throttle_classes = [UserRateThrottle, AnonRateThrottle]

@api_view(['GET'])
@permission_classes([permissions.IsAuthenticatedOrReadOnly])
@throttle_classes([AnonRateThrottle])
def BookList(request):         # function base view
    return Response("res")
```

##### c. Throttling custom
|api\
|---throttling.py
```py
class ReviewCreateThrottle(UserRateThrottle):      # custom throttle class
    scope = 'review-create'    # addition to user + anon we want to create a new scope

'DEFAULT_THROTTLE_RATES': {   # inside settings.py 
    'review-create': '5/day'    # introduce your custome scope
}
```

##### d. ScopedRateThrottle
```py
from rest_framework.throttling import ScopedRateThrottle      # another kind of custome throttling
class BookList(generics.ListAPIView):     # class base view
    queryset = Book.objects.all()          # we can combine scopedRate with custome throttle: 1/minute + 100/day
    serializer_class = BookSerializer
    throttle_classes = [ScopedRateThrottle]      # with scopedRate we can directly import witout creating any custome throttling class
    throttle_scope = 'book-list'         # in settings.py
```
### Filtering
Like `where` condition in sql
```py
class BookList(generics.ListAPIView):     
    serializer_class = BookSerializer                  # easier way to impleament filtering is to use get_queryset()
    def get_queryset(self):                            # 1. filtering against the url (directly maping the value)
        firstname = self.kwargs.get('fullname')           # browser url: api/books/fullname/  ---> django url: /<str:fullname>/
        return Book.objects.filter(author__fullname='pk')      # __firstname is used for accessing fields of foreign key
    def get_queryset(self):                           # 2. filtering against query params 
        fullname = self.request.query_params.get('fullname', None)    # browser url: api/books/?firstname=ali 
        return Book.objects.filter(author__fullname='pk')      # django url: /api/books/     ---> there is no need for mapping parameters
```

##### Generic filtering
we can only use this approach for `generic views`
```py
INSTALLED_APPS = [              #  `pip install django-filter` --> use this package for generic filtering
    'django_filters',           # what ever you install should be in INSTALLED_APPS
    'rest_framework',
]
class BookList(generics.ListAPIView):             # we should send parameters in the url 
    serializer_class = BookSerializer              # but no need for extracting them in get_queryset()
    filter_backends = [filters.DjangoFilterBackend]    # use for exact matching of fix values
    filterset_fields = ['author__fullname', 'title']  # we can send both or one of them inside url parameters
    filter_backends = [filters.SearchFilter]          # use for including matching of keywords ex: 'man' inside 'superman'
    search_fields = ['author__fullname', 'title']    # we can use regex pattern for search_fields like: '^title$'
    filter_backends = [filters.OrderingFilter]        # use for sorting 
    ordering_fields = ['rating']            # url: /api/?ordering=rating   or =-rating for decending or =rating,age for multiple ordering
```
### pagination
##### 1. global pagination
```py
REST_FRAMEWORK ={   # do not use this for object level
    'DEFAULT_PAGINATION_CLASS': 'rest_framework.pagination.LimitOffsetPagination',   # first page is our regular api but bext page wil be diff
    'PAGE_SIZE': 10   # each req gives us a json like: {count:57, next: url/api/?limit=10&offset=20,resutls=[data]}
}
```

#### 2. class pagination
|api
|---pagination.py

######  a. PageNumberPagination
```py
from rest_framework.pagination import PageNumberPagination   # recommend     # pagination works with url params
class BookListPagination(PageNumberPagination):
    page_size = 10      # default size     # first page url: /api/   ---> next url: /api/?page=2
    page_query_param = 'p'      # instead of page in url: /api/?p=2
    page_size_query_param = 'size'     # user can overwrite page_size inside url: /api/?p=3&size=20
    max_page_size = 100            # user can not overwrite page_size with larger values
    last_page_strings = ['end']     # by default we can get the last page by: /api/?page=last

class BookList(generics.ListAPIView):   
    queryset = Book.objects.all()                
    serializer_class = BookSerializer
    pagination_class = BookListPagination
```

##### b. LimitOffsetPagination
```py

from rest_framework.pagination import LimitOffsetPagination  # limit = page_size    offset= number of skip
class BookListPagination(LimitOffsetPagination):  # offset=10 means skip 10 elements and load from 11
    default_limit = 10      # url: /api/?limit=10&offset=2
    max_limit = 100          # max_page_size
    limit_query_param = 'limit'        # alternative word inside url params
    offset_query_param = 'start'        # limit and offset = start and limit
```

##### c. CursorPagination

+ we do not have `page number` anymore, we just have `next` and `previous`. 
+ user can not go to specific page number. we force user to click multiple times on `next`.
+ its depond on ordering and timing (`created` filed inside models). its neccessary.

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

##### 1. Login testing

```py
from rest_framework.test import APITestCase
from rest_framework import status
from django.urls import reverse                 # with reverse we access our urls with named_urls
from django.contrib.auth.models import User      # import user from djagno models or your models
from rest_framework.authtoken.models import Token

class RegisterTestCase(APITestCase):    # tests.py inside app folder
    def test_register(self):           # all methods of class should start with 'test'
        data = {
            "username" : "testuser",        # django creates this user temporarily
            "email" : "w5HdA@example.com",   # with self.client we can send HTTP request bcz it inherits from APITestCase
            "password" : "testpassword",
            "password2" : "testpassword"
        }
        response = self.client.post(reverse('register'),data)    # first parameter is url
        self.assertEqual(response.status_code, status.HTTP_201_CREATED)   # what you expected from response?   # 201 means Successful registration

class LoginLogoutTestCase(APITestCase):

    def setUp(self):            # we need initial setup
        self.user = User.objects.create_user(username="testuser", password="testpassword")     # create user for the class 

    def test_login(self):
        data = {
            "username" : "testuser",
            "password" : "testpassword"
        }
        response = self.client.post(reverse('login'),data)            # with this named_url it tests our views
        self.assertEqual(response.status_code, status.HTTP_200_OK)     # the self.client use self.user for login

    def test_logout(self):
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
    
    def test_book_list(self):                                # book-list and book-detail are named_urls
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
