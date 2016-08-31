---
layout: post
title:  "sorl-thumbnail을 이용해 Django 썸네일 이미지 만들기"
date:   2016-08-31 12:15:00 + 0900
categories: Study
---
<h4> sorl-thumbnail </h4>
: Django를 위한 thumbnail library


: Image를 upload할 경우 cached DB에 key-value형태로 thumbnail을 저장

[sorl-thumbnail github](https://github.com/mariocesar/sorl-thumbnail)

---

[2016-08-30자 Django REST framework](https://ham1955burger.github.io/study/2016/08/30/django-rest.html) 이어서..

<h4> 설치 하기 </h4>

* sorl-thumbnail 설치

      $ pip install sorl-thumbnail

* cache DB 설치

  * memcached 설치

        $ brew install memcached

    * memcached가 잘 설치되었는지 확인

          $ cd /usr/local/Cellar/memcached/1.4.24/bin
          $ ./memcached

          => 아무 반응 없이 커서 깜빡임

      terminal 창 새로 열어서 `telnet 127.0.0.1 11211` 로 접속 잘 되는지 확인

          $ telnet 127.0.0.1 11211
          Trying 127.0.0.1...
          Connected to localhost.
          Escape character is '^]'.
          ^]
          telnet>

  * python-memcached 설치

        $ pip install python-memcached

---

<h4> Django에 적용하기 </h4>

* python-memcached를 cached db로 설정

      {% highlight python %}
      # project_wordbook/settings.py

      INSTALLED_APPS = [
      ...,
      'sorl.thumbnail',
      ]

      CACHES = {
        'default': {
            'BACKEND': 'django.core.cache.backends.memcached.MemcachedCache',
            'LOCATION': '127.0.0.1:11211',
        }
      }
      {% endhighlight %}

* cached DB에 KVStore table 만들기

  * migration 파일 만들기

        $ python manage.py makemigrations thumbnail
        Migrations for 'thumbnail':
  	     /Users/user/Documents/Dev/temp/virtualenv/wordbook/lib/python3.5/site-packages/sorl/thumbnail/migrations/0001_initial.py:
    	    - Create model KVStore

  * migrate 하기

        $ python manage.py migrate
        Operations to perform:
  	     Apply all migrations: admin, auth, contenttypes, sessions, thumbnail, wordbook
	      Running migrations:
  	     Rendering model states... DONE
  	     Applying thumbnail.0001_initial... OK

* model 수정

  기존 Word table에 image field 추가
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
  {% endhighlight %}

  * migration 파일 만들기

        $ python manage.py makemigrations
        SystemCheckError: System check identified some issues:

        ERRORS:
        wordbook.Word.image: (fields.E210) Cannot use ImageField because Pillow is not installed.
        HINT: Get Pillow at https://pypi.python.org/pypi/Pillow or run command "pip install Pillow".

    *괜찮아. 쫄거없어*

    models.ImageField()를 사용하기 위해선 **Pillow**가 설치되어 있어야 한다.

    * Pillow 설치

          $ pip install Pillow

    다시한번 migration 파일 만들기

        $ python manage.py makemigrations
        You are trying to add a non-nullable field 'image' to word without a default; we can't do that (the database needs something to populate existing rows).
        Please select a fix:
        1) Provide a one-off default now (will be set on all existing rows with a null value for this column)
        2) Quit, and let me add a default in models.py
        Select an option:

    *괜찮아. 쫄거없어2*

    => 너 요번에 imageField 넣을거면, 기존에 Word table에 있던 값들의 imageField엔 어떤 값을 넣을꺼야? 1) 뭐 넣을지 너가 입력할래? 2) models.py을 수정할래? 난 옵션 1 선택 `Select an option: 1`

        Please enter the default value now, as valid Python
        The datetime and django.utils.timezone modules are available, so you can do e.g. timezone.now
        Type 'exit' to exit this prompt
        >>>

    => 기존에 있던 값들의 imageField에 뭐 넣을거야? `>>> 1` 1 넣을거야

        Migrations for 'wordbook':
        wordbook/migrations/0002_word_image.py:
        - Add field image to word

    *(후)*

    다음번에 마저 정리하는걸로..
