
# Intro to Django REST Framework
## API Design

For our example today, we will design a basic application called portfolio which will users create and keep track of their portfolio and projects.

Our API will have the will do the following:

Create/Read/Update/Delete Users 
Create/Read/Update/Delete Portfolio 
Create/Read/Update/Delete Projects 

REST Operations

Here's what our application will do.

GET /users/ - Retrieves all users

POST /users/ - Adds a new user

GET /users/:id/ - Retrieve a user object with corresponding id

PUT /users/:id/ - Updates a user object

DELETE /users/:id/ - Deletes a user

GET /projects/ - Retrieves all projects

etc...

We will also be able to filter, search, etc...
`
/users/?search=rudy
/projects/?users__first_name=rudy
Project Setup
`

Create a new Django project that we'll use for today and call the app portfolio. Install DRF and remember to add it to installed apps within your settings file.

Extra Python Libraries

`pip install djangorestframework`
        
Settings

`
INSTALLED_APPS = (
    ...
    "rest_framework",
    "portfolio",
)
`
        
Registrar Models

Use the models below in your project. Remember to makemigrations and migrate.

Models

`
class User(AbstractUser):
    about = models.TextField(null=True)

    def __unicode__(self):
        return u"{}".format(self.username)


class Project(models.Model):
    title = models.CharField(max_length=75)
    description = models.TextField()
    owner = models.ForeignKey(User, related_name="project")
    follower = models.ManyToManyField(User, related_name="followed_project", null=True, blank=True)

    def __unicode__(self):
        return self.title
`

Then register your models with the admin and prepopulate with information.

# API File Structure

When creating an API using Django Rest Framework, you can everything in just one file, but this can quickly become overwhelming! Below is a more ideal structure to start with to organize your code better.

Create the following api django package and empty files in the root directory of your app. We will explain what these files mean in the following slides.


`app/
    api/
        serializers.py
        views.py
        permissions.py`
        
# DRF Request & Response Flow

The files in our api directory that we just created will help us separate out our code. This also helps us better understand the Request and Response flow at each step as shown in the diagram below.


# Serializers

DRF serialization makes it easy to convert information from your native Python datatypes into JSON or XML content types and vice versa. They look and act very similar to Django forms. Much like how Django forms have Form and ModelForm classes, DRF has Serializer and ModelSerializer classes.

DRF's ModelSerializer maps closely to your model definitions and lets you create a Serializer class that corresponds to it like this:

Serializers


`from rest_framework import serializers
from portfolio.models import User, Project

class UserSerializer(serializers.ModelSerializer):
    class Meta:
        model = User
        # fields = ('id', 'username', 'first_name', 'last_name', 'email', 'is_staff', 'date_joined', 'about')
        # read_only_fields = ('date_joined',)
        # write_only_fields = ('password',)

class ProjectSerializer(serializers.ModelSerializer):
    class Meta:
        model = Project`
        
# ViewSets

Django REST framework ViewSet or View classes (may also be known as a resource), is the fundamental concept of any RESTful API. This is where you control what your dataset is, permissions and authorization on the data, etc... will be.

Let's say we want to define a simple viewset to list and retreive all users. We'd write our UserViewSet like in the example shown here.

But this method has a lot of repeat logic. DRF's ModelViewSet can help with this as we'll see in the next example.

Model ViewSets

Below is a basic DRF resource for our users and project models which inherits from DRF's ModelViewSet.

Views

`
from rest_framework import viewsets

class UserViewSet(viewsets.ModelViewSet):
    queryset = User.objects.all()
    serializer_class = UserSerializer
    # lookup_field = 'username'

class ProjectViewSet(viewsets.ModelViewSet):
    queryset = Project.objects.all()
    serializer_class = ProjectSerializer
`

# Binding ViewSets to URLs via Routers

Routers allows us to quickly tell Django and DRF about our new resource so that we can test it out. We can do this explicitly, or use DRF's Router class to handle a lot of the functionality automatically.

In urls.py we need to put the following code to tell Django to route the url /students/ to go to our new resource.

`
router = routers.DefaultRouter()
router.register(r'users', UserViewSet, base_name='users')
router.register(r'projects', ProjectViewSet, base_name='projects')

urlpatterns = patterns("",
    ...
    url(r'^', include(router.urls)), # Include router urls into our urlpatterns
    url(r'^api-auth/', include('rest_framework.urls', namespace='rest_framework')),
 )`
        
Go to the next page to see the URLs generated.

Routers (cont)

The register() methods will automatically generate the following URL patterns for us:
`
URL pattern: ^users/$ Name: 'user-list'
URL pattern: ^users/{pk}/$ Name: 'user-detail'
URL pattern: ^projects/$ Name: 'project-list'
URL pattern: ^projects/{pk}/$ Name: 'project-detail'
`

Visit the endpoints to see it in action!

Try it out!

# POSTMAN

There are many ways to consume your endpoint, but let's try using POSTMAN first.

If you don't have POSTMAN installed, you can install it as a chrome app here.

POST a new user to the API through postman.

Check that this user was created by making a GET call afterwards.

GET a single project through postman.

CURL

You can also consume your endpoints through your terminal window. Try typing in curl http://127.0.0.1:8000/users/1/ to get the data of user with id 1.

Try it out!

# Django Rest Framework's Browsable API

DRF's Browsable API is one of the many perks of working with DRF. It allows you to visualize the data being transferred through your API and will even allow you to make REST actions against these endpoints.

Go to the user api url in your browser! All of the students you entered in the Admin should show up in the DRF browsable interface.

http://127.0.0.1:8000/users/

Try updating a user resource or project resource and posting a new one.

