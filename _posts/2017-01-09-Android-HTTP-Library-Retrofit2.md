---
layout: post
title:  "Android HTTP Library - Retrofit2"
date:   2017-01-09 10:39:00 +09
categories: Android
---

검색 해보니 Volley와 Retrofit2가 많이 쓰이는 것 같다.

Volley는 Google에서 만들었고, Retrofit2는 Square에서 만들었는데,
ButterKnife를 만든 JakeWharton이 Square에 속해있고,
Retrofit2를 관리하고 있다하여. 아무 이유 없이 Retrofit2로 결정.~~(단순)~~

---

>#### Retrofit2 란?

앞서 설명했듯이 Square에서 만든 HTTP Library.

Serialization 기본으로 GSON을 쓴다. 잭쓴!으로 바꿔도 무방

[자세한 설명](https://realm.io/news/droidcon-jake-wharton-simple-http-retrofit-2/)[^1]은 생략. ~~사실 잘 모른다는...~~

[square/retrofit Github 바로가기](https://github.com/square/retrofit)

[retrofit-converters/gson Github 바로가기](https://github.com/square/retrofit/tree/master/retrofit-converters/gson)

[retrofit 공식 문서 바로가기](http://square.github.io/retrofit/)[^2]

---

>#### Retrofit2 Download

  * Gradle > Retrofit2, Gson 추가

        dependencies {
            compile 'com.squareup.retrofit2:retrofit:2.1.0'
            compile 'com.squareup.retrofit2:converter-gson:2.1.0'
        }

  * AndroidManifest.xml > Permission 추가

        <uses-permission android:name="android.permission.INTERNET"/>

---

>#### Retrofit Instance 만들기

{% highlight java %}
public class ServiceGenerator {
    public static final String BASE_URL = "http://192.168.0.9:8080";
    private static Retrofit retrofit = null;
    public static Retrofit getClient() {
        if (retrofit == null) {
            retrofit = new Retrofit.Builder()
            .baseUrl(BASE_URL)
            .addConverterFactory(GsonConverterFactory.create())
            .build();
        }
        return retrofit;
    }
}
{% endhighlight %}

>#### Retrofit2 사용하기

  여러 케이스를 겪으며 더 사용해봐야 알겠지만 기본적인 기능을 사용해보며, 뉴비가 느낀 가장 큰 장점은 parameter annotation으로 가독성이 좋아 쉽게 느껴졌다는 점과 Callback의 return type을 지정 할 수 있어, 해당 object로 자동 mapping 해준다는 점.

  [추후 링크 업데이트](https://www.google.com)

---

>#### 참고

[Comparing Retrofit 2.0 vs. Volley](http://vickychijwani.me/retrofit-vs-volley/)

[Retrofit - Getting Started and Creating an Android Client](https://futurestud.io/tutorials/retrofit-getting-started-and-android-client)

[Android Working with Retrofit HTTP Library](http://www.androidhive.info/2016/05/android-working-with-retrofit-http-library/)

[^1]: [Simple HTTP with Retrofit2 한글](https://realm.io/kr/news/droidcon-jake-wharton-simple-http-retrofit-2/)

[^2]: [retrofit 한글 문서 바로가기](http://devflow.github.io/retrofit-kr/)
