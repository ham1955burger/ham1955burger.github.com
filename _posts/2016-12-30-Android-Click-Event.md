---
layout: post
title:  "Android Click Event"
date:   2016-12-30 13:48:00 +09
categories: Android
---

단어를 입력한 뒤 버튼을 누르면, 입력한 단어가 toast로 출력되게 해보자.

![Click_Event_Layout](/assets/images/android_click_event/click_event_layout.png)

---

#### Layout을 Source code와 Bind하기

xml에 있는 view들을 source code에서 사용하고자 할땐 아래와 같이 bind해줘야 한다.

{% highlight java %}
  ...
  public class MainActivity extends Activity {
      EditText wordEditText;
      Button toastButton;

      @Override
      protected void onCreate(Bundle savedInstanceState) {
          super.onCreate(savedInstanceState);

          // Bind할 xml파일
          setContentView(R.layout.activity_main);

          // View Bind
          wordEditText = (EditText) findViewById(R.id.wordEditText);
          toastButton = (Button) findViewById(R.id.toastButton);
      }
  }
{% endhighlight %}

Bind한 xml파일의 views 중 bind할 id값을 찾아 타입에 맞게 형 변환 시켜준다.

이 id값은 xml에서 설정 한다. `android:id="@+id/toastButton"`

~~xml은 생략. ㅋ~~

---

#### Android에서 Click Event 주는 방법 4가지

1) 기본적인 방법

{% highlight java %}
  ...
  // Listener 객체 생성
  View.OnClickListener clickListener = new View.OnClickListener() {
      @Override
      public void onClick(View view) {
          showToast();
      }
  };

  // Button에 Listener 설정
  toastButton.setOnClickListener(clickListener);)
{% endhighlight %}


2) 익명 클래스를 이용한 방법

{% highlight java %}
  ...

  // Listener 객체를 익명클래스로 구현
  toastButton.setOnClickListener(new View.OnClickListener() {
      @Override
      public void onClick(View view) {
            showToast();
      }
  });
{% endhighlight %}

3) 상속을 통한 방법

{% highlight java %}
  ...
  public class MainActivity extends Activity implements View.OnClickListener {
      ...
      @Override
      protected void onCreate(Bundle savedInstanceState) {
        ...
        toastButton.setOnClickListener(this);
      }
  }

  // View.OnclickListener의 추상 메소드 onClick 구현
  @Override
  public void onClick(View view) {
      switch (view.getId()) {
          case R.id.toastButton:
          showToast();
      }
  }
{% endhighlight %}

4) 내부 클래스를 이용한 방법

{% highlight java %}
  ...
  public class MainActivity extends Activity {
      ...
      @Override
      protected void onCreate(Bundle savedInstanceState) {

          // 내부클래스 ClickListener형으로 객체 생성
          ClickListener clickListener = new ClickListener();
          // Button에 Listener 설정
          toastButton.setOnClickListener(clickListener);
      }

      // 내부클래스 ClickListener 정의
      class ClickListener implements View.OnClickListener {
          @Override
          public void onClick(View view) {
              showToast();
          }
      }

{% endhighlight %}

---

#### Toast 띄우기

{% highlight java %}
  public void showToast() {
      Toast.makeText(this,
              "입력하신 단어는 '" + wordEditText.getText() + "' 입니다.",
              Toast.LENGTH_SHORT
      ).show();
  }
{% endhighlight %}

---

~~정리하고보니.. 뭐 크게 다를게 없네...^^;~~

보통 익명 클래스를 이용한 방법을 많이 쓰는 것 같고, 나도 이 방법을 주로 쓰고있다. 편하니깐.

---

#### 참고

[자바의 추상클래스(Abstract class)와 인터페이스(interface class)](http://silverktk.tistory.com/134)
