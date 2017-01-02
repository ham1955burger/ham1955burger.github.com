---
layout: post
title:  "Android Layout Bind Library : Butter Knife"
date:   2016-12-30 13:48:00 +09
categories: Android
---

사실 ActionBarSherlock Library를 붙여 볼라다가 실패..~~다음에 붙이는걸로 (쿨럭)~~ 중 발견한 라이브러리.

Android 뉴비는 Android framework 기본 기능인줄 알았으나, library 였다는...~~(껄껄)~~

사용법도 무척 쉽고 간편하여 바로 적용해보았다.

---

>#### Butter Knife 란?

ActionBarSherlock 개발자 JakeWharton이 만든 open source library.

Butter Knife는 Layout과 source code를 보다 쉽게 bind 해준다.

String, Drawable, Color, ... 도 bind 할 수 있고,

여러 View들을 List 형태로 group화 할 수 있으며,

Listener도 간단히 bind 할 수 있다.

[JakeWharton/ButterKnife Github 바로가기](https://github.com/JakeWharton/butterknife)

[ButterKnife 공식 홈페이지 바로가기](http://jakewharton.github.io/butterknife/)

---

>#### Butter Knife Download

* app/build.gradle > dependencies에 butterknife 추가

      dependencies {
        compile 'com.jakewharton:butterknife:8.4.0'
        annotationProcessor 'com.jakewharton:butterknife-compiler:8.4.0'
      }

---

>#### Butter Knife 적용하기

* Layout Bind와 View Bind

  기존코드는 여기서 >
  [Android Click Event 바로가기](https://ham1955burger.github.io/android/2016/12/30/Android-Click-Event.html)

  {% highlight java %}
    public class MainActivity extends Activity {

      // Declaration & Bind
        @BindView(R.id.wordEditText) EditText wordEditText;
      //    @BindView(R.id.toastButton) Button toastButton;

        @Override
        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_main);
            // xml(layout) bind
            ButterKnife.bind(this);
        }

        // Set listener
        @OnClick(R.id.toastButton) void clickButton(View view) {
            Toast.makeText(this,
                    "입력하신 단어는 '" + wordEditText.getText() + "' 입니다.",
                    Toast.LENGTH_SHORT
            ).show();
        }
    }
  {% endhighlight %}

---

>#### 느낀점

기존 코드와 비교 해보면 확실히 간결해지고 깔끔해진 것을 볼 수 있다. ~~가독성이 더 좋은건 iOS와 구조가 비슷해진 탓일까요?~~

뉴비라 기본 코드를 숙지해야 할 때, 이런 라이브러리를 쓴다는게 조금 마음에 걸리긴 하지만... 앞으로 Butter knife를 적용하여 Study할 예정.
