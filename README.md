
# Intro to Django REST Framework
API Design

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
GET /projects/ - Retrieves all projectss
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

API File Structure

When creating an API using Django Rest Framework, you can everything in just one file, but this can quickly become overwhelming! Below is a more ideal structure to start with to organize your code better.

Create the following api django package and empty files in the root directory of your app. We will explain what these files mean in the following slides.


`app/
    api/
        serializers.py
        views.py
        permissions.py`
        
DRF Request & Response Flow

The files in our api directory that we just created will help us separate out our code. This also helps us better understand the Request and Response flow at each step as shown in the diagram below.
