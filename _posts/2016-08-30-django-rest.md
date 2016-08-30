---
layout: post
title:  "Django REST framework"
date:   2016-08-30 15:20:00 + 0900
categories: Study
---
<h4> Django </h4>

: Python으로 만들어진 무료 오픈소스 웹 어플리케이션 프레임워크

[Django 공식 홈페이지](https://www.djangoproject.com)

<h4> Django REST framework </h4>

: Django에서 REST API를 만들때 가장 널리 사용되는 프레임워크

[Django REST framework 공식 홈페이지](http://www.django-rest-framework.org)

---

<h4> 목표 </h4>

: Django REST Framework를 사용하여 간단한 단어장을 만들어보자.

---

<h4> Django 설치하기 </h4>

* brew 설치하기

      $ ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"

* python3 설치

      $ brew install python3

* pip 설치

      $ sudo easy_install pip

* virtualenvwrapper 설치

      $ pip install virtualenvwrapper

      Error가 난다면..

      $ sudo pip install --ignore-installed virtualenvwrapper

  Error가 나는 이유

    1) python3를 brew로 깔아서

    2) python3.x 쓸 경우 pip이 아닌 pip3을 설치해서 깔아야 한다는 소리가 있음

* virtualenvwrapper을 실행할 수 있게 profile 파일 수정

      $ vi ~/.bash_profile

      export WORKON_HOME=[WORKON_HOME_PATH]

      export VIRTUALENV_PYTHON=/usr/local/bin/python3.5

      source /usr/local/bin/virtualenvwrapper.sh

* virtualenv 생성

      $ mkvirtualenv --python=/usr/local/bin/python3.5 [virtualenv_name]

* Django 설치

      $ pip install django

---

<h4> Django REST Framework를 이용한 단어장 REST API 만들기 </h4>

* Django REST framework 설치

      $ pip install djangorestframework

* Project 만들기

      $ django-admin.py startproject project_wordbook
      $ cd project_wordbook

* App 만들기

      $ python manage.py startapp wordbook

*현재 tree 구조*

    project_wordbook/
    ├── manage.py
    ├── project_wordbook
    │   ├── __init__.py
    │   ├── __pycache__
    │   │   ├── __init__.cpython-35.pyc
    │   │   └── settings.cpython-35.pyc
    │   ├── settings.py
    │   ├── urls.py
    │   └── wsgi.py
    └── wordbook
        ├── __init__.py
        ├── admin.py
        ├── apps.py
        ├── migrations
        │   └── __init__.py
        ├── models.py
        ├── tests.py
        └── views.py

* rest_framework와 app을 project_wordbook/settings.py에 정의하기

      <!-- project_wordbook/settings.py -->

      INSTALLED_APPS = (
        ...
        'rest_framework',
        'wordbook.apps.WordbookConfig',
      )

* model 만들기

  {% highlight python %}
    # wordbook/models.py

    from django.db import models

    class Word(models.Model):
        created = models.DateTimeField(auto_now_add=True)
        word = models.CharField(max_length=100)
        mean = models.CharField(max_length=100)
        example = models.TextField()

        class Meta:
            ordering = ('created', )
  {% endhighlight %}

* model migration 파일 만들기

      $ python manage.py makemigrations wordbook
      Migrations for 'wordbook':
        wordbook/migrations/0001_initial.py:
          - Create model Word

* model migrate 하기

      $ python manage.py migrate
      Operations to perform:
        Apply all migrations: admin, auth, contenttypes, sessions, wordbook
      Running migrations:
        Rendering model states... DONE
        Applying contenttypes.0001_initial... OK
        Applying auth.0001_initial... OK
        Applying admin.0001_initial... OK
        Applying admin.0002_logentry_remove_auto_add... OK
        Applying contenttypes.0002_remove_content_type_name... OK
        Applying auth.0002_alter_permission_name_max_length... OK
        Applying auth.0003_alter_user_email_max_length... OK
        Applying auth.0004_alter_user_username_opts... OK
        Applying auth.0005_alter_user_last_login_null... OK
        Applying auth.0006_require_contenttypes_0002... OK
        Applying auth.0007_alter_validators_add_error_messages... OK
        Applying auth.0008_alter_user_username_max_length... OK
        Applying sessions.0001_initial... OK
        Applying wordbook.0001_initial... OK

* wordbook/serializers.py

  {% highlight python %}
      # wordbook/serializers.py

      from rest_framework import serializers
      from wordbook.models import Word

      class WordSerializer(serializers.ModelSerializer):
          class Meta:
              model = Word
              fields = ('pk', 'created', 'word', 'mean', 'example')
  {% endhighlight %}

* wordbook/views.py

  {% highlight python %}
      # wordbook/views.py

      from wordbook.models import Word
      from wordbook.serializers import WordSerializer
      from rest_framework import generics
      from rest_framework.response import Response

      class WordList(generics.ListCreateAPIView):
          queryset = Word.objects.all()
          serializer_class = WordSerializer

      class WordDetail(generics.RetrieveUpdateDestroyAPIView):
          queryset = Word.objects.all()
          serializer_class = WordSerializer

          def put(self, request, pk, format=None):
              wordObj = Word.objects.get(pk=pk)
             serializer = WordSerializer(wordObj, data=request.data, partial=True)
             if serializer.is_valid():
                 serializer.save()
                 return Response(serializer.data)
             return Response(serializer.errors, status=status.HTTP_400_BAD_REQUEST)
  {% endhighlight %}

* wordbook/urls.py

  {% highlight python %}
      # wordbook/urls.py

      from django.conf.urls import url
      from wordbook import views

      urlpatterns = [
          url(r'^wordbook/$', views.WordList.as_view()),
          url(r'^wordbook/(?P<pk>[0-9]+)/$', views.WordDetail.as_view()),
      ]
  {% endhighlight %}

* project_wordbook/urls.py

  {% highlight python %}
      from django.conf.urls import url, include

      urlpatterns = [
          url(r'^', include('wordbook.urls')),
      ]
  {% endhighlight %}

  *현재 폴더 구조*

      project_wordbook/
      ├── db.sqlite3
      ├── manage.py
      ├── project_wordbook
      │   ├── __init__.py
      │   ├── __pycache__
      │   │   ├── __init__.cpython-35.pyc
      │   │   ├── settings.cpython-35.pyc
      │   │   ├── urls.cpython-35.pyc
      │   │   └── wsgi.cpython-35.pyc
      │   ├── settings.py
      │   ├── urls.py
      │   └── wsgi.py
      └── wordbook
          ├── __init__.py
          ├── __pycache__
          │   ├── __init__.cpython-35.pyc
          │   ├── admin.cpython-35.pyc
          │   ├── apps.cpython-35.pyc
          │   ├── models.cpython-35.pyc
          │   ├── serializers.cpython-35.pyc
          │   ├── urls.cpython-35.pyc
          │   └── views.cpython-35.pyc
          ├── admin.py
          ├── apps.py
          ├── migrations
          │   ├── 0001_initial.py
          │   ├── __init__.py
          │   └── __pycache__
          │       ├── 0001_initial.cpython-35.pyc
          │       └── __init__.cpython-35.pyc
          ├── models.py
          ├── serializers.py
          ├── tests.py
          ├── urls.py
          └── views.py

* server 구동하기

      $ python manage.py runserver
      Performing system checks...

      System check identified no issues (0 silenced).
      August 30, 2016 - 08:55:55
      Django version 1.10, using settings 'project_wordbook.settings'
      Starting development server at http://127.0.0.1:8000/
      Quit the server with CONTROL-C.

  : 127.0.0.1:8000/wordbook/으로 접속하여 아래와 같은 page가 나오면 성공

  ![wordList](/assets/images/django-rest/wordList.png)


* POST

  단어를 등록해보자.

  하단에 Content 입력 부분에 값 입력 후 POST button 클릭

  ![post](/assets/images/django-rest/post.png)

  아래와 같이 단어가 등록된 것을 볼 수 있다.

  ![post_success](/assets/images/django-rest/post_success.png)

* PUT

  방금 등록한 단어의 example을 수정해보자.

  등록한 hamburger의 pk는 1인 것을 알 수 있다. 수정하기 위해 상세 url로 접속한다.


  : 127.0.0.1:8000/wordbook/1

  ![put](/assets/images/django-rest/put.png)

  example이 바뀐 것을 볼 수 있다.

  ![put_success](/assets/images/django-rest/put_success.png)

* DELETE

  단어를 삭제해보도록 하자. delete button 클릭

  ![delete](/assets/images/django-rest/delete.png)

  ![delete_confirm](/assets/images/django-rest/delete_confirm.png)

  기존 pk 1번의 주소로 접속할 경우 (: 127.0.0.1:8000/wordbook/1),

  하단 처럼 Not found가 뜨는 것을 볼 수 있다.

  ![not_found](/assets/images/django-rest/not_found.png)

  List로도 접속 해보자 (: 127.0.0.1:8000/wordbook/)

  ![empty_list](/assets/images/django-rest/empty_list.png)

  역시 삭제된 것을 볼 수 있다.
