---
layout: post
title:  "Heroku Python"
date:   2016-08-30 11:45:00 + 0900
categories: Study
---
<h4> Heroku Python (Django REST) </h4>
: PaaS(개발을 위한 플랫폼 구축을 할 필요 없이 필요한 개발 요소들을 웹에서 쉽게 빌려쓸 수 있게 하는 모델)

---
<h4> 사용준비 </h4>
* Heroku 회원가입

  [Heroku 공식 홈페이지](https://www.heroku.com/)

* Pip설치

  [ http://codingdojang.com/scode/371]( http://codingdojang.com/scode/371)

* Virtualenv 설치(= virtualenvwrapper)

  [https://github.com/kennethreitz/python-guide/blob/master/docs/dev/virtualenvs.rst](https://github.com/kennethreitz/python-guide/blob/master/docs/dev/virtualenvs.rst)

* Postgres 설치

  a. brew 설치

      $ /usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)”

  b. brew를 이용한 postgres 설치

      $ brew install postgresql


* Toolbelt 설치

  [ https://s3.amazonaws.com/assets.heroku.com/heroku-toolbelt/heroku-toolbelt.pkg](https://s3.amazonaws.com/assets.heroku.com/heroku-toolbelt/heroku-toolbelt.pkg)

  ---

<h4> 시작하기 </h4>

* 로그인

      $ heroku login
      Enter your Heroku credentials.
      Email: [Your heroku account]
      Password (typing will be hidden):
      Logged in as [Your heroku account]

* Git에서 소스코드 clone하기

      $ git clone [repository_url]
      $ cd [clone_folder]

  **주의!** Heroku를 사용하기 위해선 아래와 같은 파일이 추가 및 수정되어야 함

    * 추가할 파일(<u>App의 root에 위치</u>)

      * Procfile : Heroku에 Django app을 돌리기 위한 파일

            <!-- Procfile -->

            web: gunicorn [app_name].wsgi --log-file -

      * requirements.txt : 필요한 dependency 모음 파일

            <!-- requirements.txt -->

            .
            .
            .

            psycopg2==2.6.1
            dj-database-url==0.3.0
            gunicorn==19.6.0

      * runtime.txt : 실행전 컴파일 파일. 기본으로 python2.7(?)이 도는데 다른 버전이면 추가하여 그 버전으로 돌게 해야함

        현재 내 Django REST project는 python v3.5.2를 사용

            <!-- runtime.txt -->

            python-3.5.2

      * app.json : ..모름...기본적인 정보 정의해주는거 같은데 없어도 무관하였음

            <!-- app.json -->

            {
              "name": "[project_name]",
              "description": "[description]",
              "image": "heroku/python",
              "repository": "[repository_url]",
              "keywords": ["python", "django"],
              "addons": ["heroku-postgresql"]
            }

    * 수정할 파일

      * App/settings.py : 수정

              {% highlight python %}
              # App/settings.py

              STATIC_ROOT = os.path.join(BASE_DIR, 'collected_statics')

              os.environ.get('HEROKU'):  # heroku config:set HEROKU=1
              import dj_database_url
              DATABASES['default'] = dj_database_url.config()
              {% endhighlight %}

* Heroku 생성

      $ heroku create [heroku_app_name]
      $ git push heroku master

* Heroku로 app실행하기

      $ heroku open --app [heroku_app_name]

* Heroku log보기

      $ heroku logs --tails

---

<h4> 그 밖에 </h4>

내 계정 app보기 : heroku apps (5개 이상 돈내라고 나옴)

    $ heroku apps

app지우기 : heroku apps:destroy [app_name]

    $ heroku apps:destroy [app_name]

python build pack 설정하기

    $ heroku --buildpack:set heroku/python

현재 설정된 buildpack보기

    $ heroku buildpacks --app [heroku_app_name]


heroku 안에서도 돌릴수 있다!

    $ heroku run python manage.py makemigrations
    $ heroku run pip freeze


heroku 자동으로 migrate 해주기. (buildpack 추가) => 되는지 확인 해봐야되는데…. 되는거 같기도하고……. migration파일을 올려줘야하는지도 test해봐야함 올려줘야하는거 같음

    $ heroku buildpacks:add https://github.com/andreipetre/heroku-buildpack-django-migrate


heroku H10 503 ERROR

    2016-08-30T02:33:16.371966+00:00 heroku[router]: at=error code=H10 desc="App crashed" method=GET path="/favicon.ico" host=hamtest.herokuapp.com request_id=879296aa-e123-49ae-8cc8-cb27ec349783 fwd="183.98.145.15" dyno= connect= service= status=503 bytes=

-> 이거 나같은 경우는 1. Procfile에 설정을 잘못해놨을때 떴었고, 2.python gunicorn으로 돌아가는건데(?) gunicorn을 안깔았을때 떴었음


heroku git쪽 같은 경우는 자세히 보지 않았으므로(문제도 없었고) 추후 필요할때 찾아보기로 하자.

    $ git remote -v
    $ heroku git:remote -a [heroku_app_name]


[Heroku 에서 제공해주는 서비스](https://elements.heroku.com/addons)
