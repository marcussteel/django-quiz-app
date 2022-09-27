# DJANGO-QUIZ-APP

```bash
# CREATING VIRTUAL ENVIRONMENT
# windows 👇
python -m venv env
# linux / Mac OS 👇
vitualenv env

# ACTIVATING ENVIRONMENT
# windows 👇
source env/Scripts/activate
# linux / Mac OS 👇
source env/bin/activate

# PACKAGE INSTALLATION
# if pip does not work try pip3 in linux/Mac OS
pip install django
# alternatively python -m pip install django
pip install python-decouple
pip freeze > requirements.txt
django-admin --version
django-admin startproject main .
```

```bash
# 💨 If you already have a requirement.txt file, you can install the packages in the file
# 💨 by entering the following commands respectively in the terminal 👇
1-python -m venv env
2-source env/Scripts/activate
3-pip install -r requirements.txt 🚀
4-python.exe -m pip install --upgrade pip
5-python manage.py migrate
6-python manage.py createsuperuser
7-python manage.py runserver
```
## 🚩 .gitignore

✔ Add a gitignore file at same level as env folder, and check that it includes .env and /env lines.

🔹 [On this page](https://www.toptal.com/developers/gitignore) you can create "gitignore files" for your projects.

## 🚩 Python Decouple

✔ Create a new file and name as .env at same level as env folder

✔ Copy your SECRET_KEY from settings.py into this .env file. Don't forget to remove quotation marks and blanks from SECRET_KEY

```
SECRET_KEY=-)=b-%-w+0_^slb(exmy*mfiaj&wz6_fb4m&s=az-zs!#1^ui7j
```

✔ Go to settings.py, make amendments below 👇

```python
from decouple import config

SECRET_KEY = config('SECRET_KEY')
```

💻 Go to terminal 👇

```bash
python manage.py migrate
python manage.py runserver
```

✔ Click the link with CTRL key pressed in the terminal and see django rocket 🚀.

## 🚩 INSTALLING DJANGO REST

💻 Go to terminal 👇

```bash
python manage.py makemigrations
python manage.py migrate
pip install djangorestframework
```

✔ Go to settings.py and add 'rest_framework' app to INSTALLED_APPS

## 🚩 ADDING AN APP

💻 Go to terminal 👇

```
python manage.py startapp quiz
```

✔ Go to settings.py and add 'quiz' app to INSTALLED_APPS

✔ Go to quiz.models.py 👇

## 🚩 ADDING MODEL

```python
from django.db import models

class UpdateCreateDate(models.Model):
    created = models.DateTimeField(auto_now_add=True)
    updated = models.DateTimeField(auto_now = True)

    class Meta:
        abstract = True
        #! We used abstract to inherit repeating lines from one place. 👆
class Category(models.Model):
    name = models.CharField(max_length=50, verbose_name='Category Name')

    def __str__(self):
        return self.name

    class Meta:
        verbose_name_plural = 'Categories'

class Quiz(UpdateCreateDate):
    title = models.CharField(max_length=50, verbose_name = 'Quiz Title')
    category = models.ForeignKey(Category, on_delete = models.CASCADE)
    #? delete questions when category changes (models.CASCADE) 👆


    def __str__(self):
        return self.title

    class Meta:
        verbose_name_plural = 'Quizzes'

class Question(UpdateCreateDate):
    SCALE = (
        ('B', 'Beginner'),
        ('I', 'Intermadiate'),
        ('A', 'Advanced')
    )
    title = models.TextField()
    quiz = models.ForeignKey(Quiz, on_delete = models.CASCADE)
    difficulty = models.CharField(max_length = 1, choices = SCALE)


    def __str__(self):
        return self.title

class Option(UpdateCreateDate):
    option_text = models.CharField(max_length = 200)
    question = models.ForeignKey(Question, on_delete = models.CASCADE)
    is_right = models.BooleanField(default=False)


    def __str__(self):
        return self.option_text
```

💻 Go to terminal 👇

```bash
python manage.py makemigrations
python manage.py migrate
```


✔ Go to quiz.admin.py for registiration 👇

```python
from django.contrib import admin
from .models import (
    Category,
    Quiz,
    Question,
    Option,
)
# Register your models here.
admin.site.register(Category)
admin.site.register(Quiz)
admin.site.register(Question)
admin.site.register(Option)
```

## 🚩 A third party app to create nested table structure 👉 [django-nested-admin](https://django-nested-admin.readthedocs.io/en/latest/quickstart.html) 👇

💻 Go to terminal

```
pip install django-nested-admin
```
## 🚩 Go to settings.py and add 'nested_admin' app to INSTALLED_APPS

## 🚩 Go to main.urls.py and add nested_admin path 👇
```python
from django.contrib import admin
from django.urls import path, include

urlpatterns = [
    path('admin/', admin.site.urls),
    path('nested_admin/', include('nested_admin.urls')),
```

## 🚩 Go to quiz.admin.py and add 👇

✔ Suppose the customer made a request: ✏"When I enter the admin panel, I can enter the quiz category, quiz title, quiz questions and answers in one place" ✏
As a developer, we customize the admin panel according to the customer's request.

```python
import nested_admin
class OptionAdmin(nested_admin.NestedTabularInline):
    model = Option
    extra = 5

class QuestionAdmin(nested_admin.NestedTabularInline):
    model = Question
    inlines = [OptionAdmin]
    extra = 5
    max_num = 20


class QuizAdmin(nested_admin.NestedModelAdmin):
    model = Quiz
    inlines = [QuestionAdmin]

admin.site.register(Quiz, QuizAdmin)
```

## 🚩 Go to quiz.views.py and add CategoryList 👇
```python
from rest_framework import generics
from .models import (
    Category,
    Quiz,
    Question,
    Option
)
from .serializers import (
    CategorySerialzier,
)

class CategoryList(generics.ListAPIView):
    queryset = Category.objects.all()
    serializer_class = CategorySerialzier
```

## 🚩 SERIALIZER FOR CATEGORY MODEL

✔ Create serializers.py under quiz

```python
from rest_framework import serializers
from .models import (
    Category,
    Quiz,
    Question,
    Option
)

class CategorySerialzier(serializers.ModelSerializer):
    class Meta:
        model = Category
        fields = (
            'id',
            'name',
            'quiz_count'
        )

```
## 🚩 Let's write a function to show the user how many quizzes there are. Let's go to models.py for that. Here "quiz_set" will refer to its submodel (Quiz)

```python
class Category(models.Model):
    @property
    def quiz_count(self):
        return self.quiz_set.count()
```

## 🔴 What is the @property decorator in Python? 👆

🔹 @property decorator is a built-in decorator in Python which is helpful in defining the properties effortlessly without manually calling the inbuilt function property(). Which is used to return the property attributes of a class from the stated getter, setter and deleter as parameters.

## 🚩 Time to create urls.py under quiz app to add the view we wrote 👇

```python
from django.urls import path
from .views import (
    CategoryList
)

urlpatterns = [
    path('', CategoryList.as_view()),
]
```

## 🚩 Go to main.urls.py and add this path 👇
```python
 path('quiz/', include('quiz.urls')),
```

## 🚩 Go to quiz.views.py and add QuizList view 👇
```python
class QuizList(generics.ListAPIView):
    queryset = Quiz.objects.all()
    serializer_class = QuizSerializer
```

## 🚩 We need a serializer for this view. So go to quiz.serializers.py and add "QuizSerializer"👇
```python
class QuizSerializer(serializers.ModelSerializer):
    category = serializers.StringRelatedField()
    class Meta:
        model = Quiz
        fields = (
            'id',
            'title',
            'category',
            'question_count',
        )
```
## 🔴 [StringRelatedField()](https://www.django-rest-framework.org/api-guide/relations/#stringrelatedfield) 👆
🔹 StringRelatedField may be used to represent the target of the relationship using its __str__ method. For example, the following serializer:
```python
class AlbumSerializer(serializers.ModelSerializer):
    tracks = serializers.StringRelatedField(many=True)

    class Meta:
        model = Album
        fields = ['album_name', 'artist', 'tracks']
```
🔹 Would serialize to the following representation:
```python
{
    'album_name': 'Things We Lost In The Fire',
    'artist': 'Low',
    'tracks': [
        '1: Sunflower',
        '2: Whitetail',
        '3: Dinosaur Act',
        ...
    ]
}
```
🔹 This field is read only.

## 🚩 To notify the user of the number of questions, add the following to models.py 👇
```python
    @property
    def question_count(self):
        return self.question_set.count()
```

## 🚩 Go to quiz.urls.py and add the 'QuizList' path 👇
```python
path('quiz/', QuizList.as_view()),
```

## 🚩We can download djago-filter to be able to filter 👇
💻 Go to terminal

```
pip install django-filter
```
## 🚩 Go to settings.py and add 'django_filters' to INSTALLED_APPS


## 🚩 Go to quiz.views.py and customize QuizList 👇
```python
from django_filters.rest_framework import DjangoFilterBackend
from rest_framework import filters

class QuizList(generics.ListAPIView):
    queryset = Quiz.objects.all()
    serializer_class = QuizSerializer
    filter_backends = [DjangoFilterBackend, filters.SearchFilter]
    filterset_fields = ['category']
    search_fields = ['title']
```

## 🚩 Create QuestionList view here 👇
```python
class QuestionList(generics.ListAPIView):
    queryset = Question.objects.all()
    serializer_class = QuestionSerializer
```

## 🚩 Go to "Option" model in quiz.models.py and add "related_name" to "question" 👇
```python
 question = models.ForeignKey(Question, on_delete=models.CASCADE, related_name='options')
```

## 🔴 related_name() 👆
🔹 The related_name attribute specifies the name of the reverse relation from the User model back to your model. If you don't specify a related_name, Django automatically creates one using the name of your model with the suffix _set. Syntax: field_name = models.Field(related_name="name")

## 🚩 We need a serializer for QuestionList view. So go to quiz.serializers.py and add "OptionSerializer" and "QuestionSerializer" 👇
```python
class OptionSerializer(serializers.ModelSerializer):
    class Meta:
        model = Option
        fields = (
            'id',
            'option_text',
            'is_right'
        )

class QuestionSerializer(serializers.ModelSerializer):
    options = OptionSerializer(many=True) #! options, refers to "related_name"
    quiz = serializers.StringRelatedField
    class Meta:
        model = Question
        fields = (
            'id',
            'quiz',
            'title',
            'options',
            'difficulty'
        )
```
## 🚩 Add the "question" path to quiz.urls.py 👇
```python
 path('question/', QuestionList.as_view()),
```

## 🚩 Go to quiz.views.py and add filter fields to "QuestionList" 👇
```python
 filter_backends = [DjangoFilterBackend, filters.SearchFilter]
 filterset_fields = ['quiz', 'difficulty']
```