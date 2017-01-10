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

>#### Retrofit2 사용하기

Retrofit2를 이용해 아래와 같은 GET방식의 간단한 서버 통신을 해보자.

![get_household_account_book](/assets/images/android-http-library-retrofit2/get_household_account_book.png)

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

---

>#### Model Class 만들기

Callback의 return type을 지정 할 수 있어, 해당 object로 자동 mapping 해주므로 API에 맞는 Data Model Class 생성.

{% highlight java %}
public class HouseholdAccountBook implements Serializable {
    // api field 값
    @SerializedName("pk")
    // 내가 사용할 변수명
    private int pk;
    @SerializedName("state") private String state;
    @SerializedName("price") private int price;
    @SerializedName("date") private String date;
    @SerializedName("category") private String category;
    @SerializedName("memo") private String memo;

    ...
    // setter and getter method
}
{% endhighlight %}

---

>#### Interface 만들기

Retrofit은 annotation와 method를 이용해 요청에 대한 정보를 서술한다.

{% highlight java %}
public interface InterfaceAPI {

    // protocol 방식과, BASE_URL을 제외한 나머지 url
    @GET("/list/")
    // Method생성 /Callback의 return type지정 -> ArrayList<HouseholdAccountBook>
    Call<ArrayList<HouseholdAccountBook>> getHABList();
}
{% endhighlight %}

---

>#### 서버에 요청 및 응답 받기

{% highlight java %}

InterfaceAPI apiService = ServiceGenerator.getClient().create(InterfaceAPI.class);

    // interface에 서술되어 있는데로 서버에 요청
    Call<ArrayList<HouseholdAccountBook>> call = apiService.getHABList();

    // 비동기 방식으로 callback 받음(응답)
    call.enqueue(new Callback<ArrayList<HouseholdAccountBook>>() {
        @Override
        public void onResponse(Call<ArrayList<HouseholdAccountBook>> call, Response<ArrayList<HouseholdAccountBook>> response) {
            // 출력결과 : OK
            Log.d(TAG, String.valueOf(response.message()));
            // 출력결과 : 200
            Log.d(TAG, String.valueOf(response.code()));

            ArrayList<HouseholdAccountBook> list = response.body();
            // 츨력결과 : 3
            Log.d(TAG, String.valueOf(list.size());
        }
        @Override
        public void onFailure(Call<ArrayList<HouseholdAccountBook>> call, Throwable t) {
            Log.e(TAG, t.toString());
        }
    });

{% endhighlight %}

---

여러 케이스를 겪으며 더 사용해봐야 알겠지만 간단한 GET 기능을 사용해보며, 뉴비가 느낀 가장 큰 장점은 parameter annotation으로 가독성이 좋다는 점과 Callback의 return type을 지정 및 자동 mapping 이었다.

---

>#### Retrofit2 더 알아보기

  [추후 링크 업데이트](https://www.google.com)

---

>#### 참고

[Comparing Retrofit 2.0 vs. Volley](http://vickychijwani.me/retrofit-vs-volley/)

[Retrofit - Getting Started and Creating an Android Client](https://futurestud.io/tutorials/retrofit-getting-started-and-android-client)

[Android Working with Retrofit HTTP Library](http://www.androidhive.info/2016/05/android-working-with-retrofit-http-library/)

[^1]: [Simple HTTP with Retrofit2 한글](https://realm.io/kr/news/droidcon-jake-wharton-simple-http-retrofit-2/)

[^2]: [retrofit 한글 문서 바로가기](http://devflow.github.io/retrofit-kr/)
