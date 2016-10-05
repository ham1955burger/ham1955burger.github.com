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

: Django REST Framework를 사용하여 이미지가 있는 단어장을 만들어보자.

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

*현재 폴더 구조*

    project_wordbook/
    ├── manage.py
    ├── project_wordbook
    │   ├── __init__.py
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

* project_wordbook/settings.py
      {% highlight python %}
        # project_wordbook/settings.py

        ...
        IMAGE_SERVER_URL = 'http://127.0.0.1:8000' # image server url 설정
        MEDIA_URL = '/uploads/' # image 접근 url 설정
        MEDIA_ROOT = os.path.join(BASE_DIR, 'uploaded_files') # image 경로 설정
        ...
        INSTALLED_APPS = (
          ...
          'rest_framework', # rest_framework 추가
          'wordbook.apps.WordbookConfig', # app 추가
        )
        ...
      {% endhighlight %}

* model 만들기

  {% highlight python %}
    # wordbook/models.py

    from django.db import models

    class Word(models.Model):
        created = models.DateTimeField(auto_now_add=True)
        word = models.CharField(max_length=100)
        mean = models.CharField(max_length=100)
        example = models.TextField()
        image = models.ImageField()

        class Meta:
            ordering = ('created', )

        def delete(self, *args, **kwargs):
              # 파일 경로에 있는 사진 제거
              self.image.delete()
              super(Word, self).delete(*args, **kwargs)
  {% endhighlight %}

* model migration 파일 만들기

      $ python manage.py makemigrations wordbook
      SystemCheckError: System check identified some issues:

      ERRORS:
      wordbook.Word.image: (fields.E210) Cannot use ImageField because Pillow is not installed.
      HINT: Get Pillow at https://pypi.python.org/pypi/Pillow or run command "pip install Pillow".

  *괜찮아. 쫄거없어*

  models.ImageField()를 사용하기 위해선 **Pillow**가 설치되어 있어야 한다.

  * Pillow 설치

        $ pip install Pillow

* 다시 한번 migration 파일 만들기

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
    import os
    from django.conf import settings

    class WordSerializer(serializers.Serializer):
      pk = serializers.IntegerField(read_only=True)
      created = serializers.DateTimeField(read_only=True)
      word = serializers.CharField(required=True, max_length=100)
      mean = serializers.CharField(max_length=100)
      # example은 model에서 TextField()로 정의되어 있지만,
      # serializers는 TextField()를 지원하지 않으므로 CharField()로 변경
      example = serializers.CharField()
      image = serializers.ImageField(write_only=True)
      image_url = serializers.SerializerMethodField('get_image', read_only=True)

      def get_image(self, obj):
          imageUrl = obj.image.url
          return settings.IMAGE_SERVER_URL + imageUrl

      def create(self, validated_data):
          return Word.objects.create(**validated_data)

      def update(self, instance, validated_data):
          if instance.image != validated_data.get('image', instance.image):
              # 기존 image와 현재 image가 다르면 파일 경로에서 기존 image 삭제
              os.remove(os.path.join(settings.MEDIA_ROOT, instance.image.path))

          instance.word = validated_data.get('word', instance.word)
          instance.mean = validated_data.get('mean', instance.mean)
          instance.example = validated_data.get('example', instance.example)
          instance.save()
          return instance
  {% endhighlight %}

* wordbook/views.py

  {% highlight python %}
      # wordbook/views.py

      from wordbook.models import Word
      from wordbook.serializers import WordSerializer
      from rest_framework import generics
      from rest_framework.response import Response
      from rest_framework import status

      class WordList(generics.ListCreateAPIView):
          queryset = Word.objects.all()
          serializer_class = WordSerializer

          def post(self, request):
                serializer = WordSerializer(data=request.data)
                if serializer.is_valid():
                    serializer.save()
                    return Response(serializer.data,
                                    status=status.HTTP_201_CREATED)
                return Response(serializer.errors,
                                status=status.HTTP_400_BAD_REQUEST)

      class WordDetail(generics.RetrieveUpdateDestroyAPIView):
          queryset = Word.objects.all()
          serializer_class = WordSerializer

          def put(self, request, pk, format=None):
              wordObj = Word.objects.get(pk=pk)
              # partial=True 옵션을 주지 않으면,
              # 필요한 모든 field의 값을 주어야 수정이 가능하다.
              serializer = WordSerializer(wordObj,
                                          data=request.data,
                                          partial=True)
              if serializer.is_valid():
                  serializer.save()
                  return Response(serializer.data)
              return Response(serializer.errors,
                              status=status.HTTP_400_BAD_REQUEST)

          def delete(self, request, pk, format=None):
              wordObj = Word.objects.get(pk=pk)
              wordObj.delete()
              return Response(status=status.HTTP_404_NOT_FOUND)
  {% endhighlight %}

* wordbook/urls.py

  {% highlight python %}
      # wordbook/urls.py

      from django.conf.urls import url
      from wordbook import views
      from django.conf.urls.static import static
      from django.conf import settings

      urlpatterns = [
          url(r'^wordbook/$', views.WordList.as_view()),
          url(r'^wordbook/(?P<pk>[0-9]+)/$', views.WordDetail.as_view()),
      ]

      if settings.DEBUG:
          urlpatterns += static(
              # MEDIA_URL로 image 접근 가능하게 함
              settings.MEDIA_URL, document_root=settings.MEDIA_ROOT
          )
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
      │   ├── settings.py
      │   ├── urls.py
      │   └── wsgi.py
      └── wordbook
          ├── __init__.py
          ├── admin.py
          ├── apps.py
          ├── migrations
          │   ├── 0001_initial.py
          │   └── __init__.py
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

  setting에 설정한 데로 이미지 url은 `/upload/`인 것을 볼 수 있다

  ![post_success](/assets/images/django-rest/post_success.png)

  *현재 폴더 구조*

  *setting에 설정한 데로 `root/uploaded_files/1955burger.png`의 경로로 이미지가 upload된 것을 볼 수 있다*

      project_wordbook/
      ├── db.sqlite3
      ├── manage.py
      ├── project_wordbook
      │   ├── __init__.py
      │   ├── __pycache__
      │   ├── settings.py
      │   ├── urls.py
      │   └── wsgi.py
      ├── uploaded_files
      │   └── 1955burger.png
      └── wordbook
          ├── __init__.py
          ├── __pycache__
          ├── admin.py
          ├── apps.py
          ├── migrations
          │   ├── 0001_initial.py
          │   ├── 0002_word_image.py
          │   ├── __init__.py
          │   └── __pycache__
          ├── models.py
          ├── serializers.py
          ├── tests.py
          ├── urls.py
          └── views.py

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

  또, root/uploaded_files/ 경로에 가보면 1955burger.png도 삭제된 것도 볼 수 있다.
