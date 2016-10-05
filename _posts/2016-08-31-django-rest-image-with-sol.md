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

  * KVStore table이 잘 만들어졌는지 확인하기

    * sqlite3 접속

          $ sqlite3 db.sqlite3

    * sqlite3 table 목록 보기

      **thumbnail_kvstore** table이 생성된 것을 볼 수 있다.

          sqlite> .tables
          auth_group                  django_admin_log          
          auth_group_permissions      django_content_type       
          auth_permission             django_migrations         
          auth_user                   django_session            
          auth_user_groups            thumbnail_kvstore         
          auth_user_user_permissions  wordbook_word

* models.py

  model schema가 바뀐게 아니므로, migrate 할 필요 없다.

  {% highlight python %}
    # wordbook/models.py

    ...
    from sorl.thumbnail import delete

    class Word(models.Model):
      ...
      def delete(self, *args, **kwargs):
            # 순서주의! cached db에서 지워준 후, 본 db에서 삭제
            delete(self.image)
            # 파일 경로에 있는 사진 제거
            self.image.delete()
            super(Word, self).delete(*args, **kwargs)
  {% endhighlight %}

* serializers.py
  {% highlight python %}
    # wordbook/models.py
    ...
    from sorl.thumbnail import get_thumbnail
    from sorl.thumbnail import delete

    class WordSerializer(serializers.Serializer):
      ...
      image_thumb_file = serializers.SerializerMethodField('get_thumb',
                                                          read_only=True)

      def get_thumb(self, obj):
          # 100x100의 size 으로 thumbail 가져오기
          thumbUrl = get_thumbnail(obj.image, '100x100',
                                  crop='center', quality=99).url
          return settings.IMAGE_SERVER_URL + thumbUrl
      ...
      def update(self, instance, validated_data):
            if instance.image != validated_data.get('image', instance.image):
                # 기존 image과 현재 image이 다르면 파일 경로에서 기존 image 삭제
                os.remove(os.path.join(settings.MEDIA_ROOT,
                                      instance.image.path))
                # cached db에 있는 thumbnail도 제거
                delete(instance.image)
            ...

  {% endhighlight %}

  * 확인하기

    데이터를 생성한뒤 response값을 보면, `image_thumb_url`이 추가 된 것을 볼 수 있다.

    ![sorl_thumbnail_success](/assets/images/django-rest-image-with-sorl/sorl_thumbnail_success.png)

    cache db에 생성된 값이며, /uploaded_files 하위에 thumb file이 존재하게 된 것을 볼 수 있다.

    ![sorl_thumbnail_folder](/assets/images/django-rest-image-with-sorl/sorl_thumbnail_folder.png)

    `image_url`과 `image_thumb_url`의 경로로 접속하면,

    `image_url`은 원본, `image_thumb_url`은 100x100의 이미지로 접근 가능 한 것을 볼 수 있다.

    또, sqlite로 접속하여 thumbnail_kvstore의 데이터를 조회하여보면

    아래와 같이 key/value 형태로 데이터가 저장되어있는 것을 볼 수 있다.

        sqlite> select * from thumbnail_kvstore;
        sorl-thumbnail||image||4979b760fffcde37c05a435b9e4c66d4|{"name": "apple.jpg", "size": [420, 288], "storage": "django.core.files.storage.FileSystemStorage"}
sorl-thumbnail||image||2eb85afd5cab0441d74315a9947977c3|{"name": "cache/fd/0c/fd0c84549a45d0627ba2a9627f094913.jpg", "size": [100, 100], "storage": "django.core.files.storage.FileSystemStorage"}
sorl-thumbnail||thumbnails||4979b760fffcde37c05a435b9e4c66d4|["2eb85afd5cab0441d74315a9947977c3"]

    데이터 삭제 후 폴더 구조와 thumbnail_kvstore의 데이터를 조회 해보면 깨끗하게 지워진 것도 확인 할 수 있다.
