---
layout: post
title:  "Android Retrofit2 Annotation"
date:   2017-01-13 14:27:00 +09
categories: Android
---

이번 글에선 Retrofit2 Annotation에 대해 좀 더 알아보자.

---

#### 연관글

* 이어진 글) [Android HTTP Library - Retrofit2](https://ham1955burger.github.io/android/2017/01/09/Android-HTTP-Library-Retrofit2.html)

* ##### Android Retrofit2 Annotation

---

>#### @PATH

보통 리스트 중 1개 항목을 선택하여 상세정보를 요청할 때,
또는 현재 로그인한 회원이 일반 회원인지 관리자인지에 따라 **URL을 유동적으로 바꿔주어야 할때**가 있다.
예를들어 아래와 같은 경우.

* #### API URL : list/1

  {% highlight java %}
  // define interface method
  @GET("/list/{pk}")
  Call<ResponseBody> listDetail(@Path("pk") int pk);
  {% endhighlight %}

  {% highlight java%}
  InterfaceAPI apiService = ServiceGenerator.getClient().create(InterfaceAPI.class);
  // calling
  apiService.listDetail(1)
  {% endhighlight %}

* #### API URL : list/semi+admin

  특수문자를 보낼 경우 이상한 문자가 섞여서 서버로 전송되는 경우가 있다.
  예를 들어, semi+admin 전송했으나 semi%2Badmin으로 전송되는 경우.

  문제는 URL Encoding때문인데, Retrofit `@Path`엔 **이미 encode되었는지 여부를 설정**하여 서버에 전송할 수 있다. (default는 false)

++ **encoded 되어있지 않은 경우**

  {% highlight java %}
  // define interface method
  @GET("/list/{user_type}")
  Call<ResponseBody> notAlreadyEncoded(
      @Path("user_type") String userType
  );
  {% endhighlight %}

  {% highlight java%}
  InterfaceAPI apiService = ServiceGenerator.getClient().create(InterfaceAPI.class);
  // calling
  apiService.notAlreadyEncoded("semi+admin") // yields /list/semi%2Badmin
  {% endhighlight %}

++ **이미 encoded 된 경우**

  {% highlight java %}
  // define interface method
  @GET("/list/{user_type}")
  Call<ResponseBody> alreadyEncoded(
      @Path(value="user_type", encoded=true) String userType
  );
  {% endhighlight %}

  {% highlight java%}
  InterfaceAPI apiService = ServiceGenerator.getClient().create(InterfaceAPI.class);
  // calling
  apiService.alreadyEncoded("semi+admin") // yields /list/semi+admin
  {% endhighlight %}

**Tip!)** Path의 type이 String인 경우 **""(공백)이 가능**하다. null은 불가능.
서버에서 /list 와 /list/ 모두 지원할때 쓸만하다.
하지만, URL이 /list/{user_type}/kind 일 경우, ""을 쓰게되면 /list//kind가 되므로 쓸 수 없다.

---

>#### @Query

  HTTP GET Protocol에서 Parameter를 줄때 쓰인다.

* #### API URL : /list?page=1

  {% highlight java %}
  // define interface method
  @GET("/list")
  Call<ResponseBody> listPage(
      @Query("page") int page
  );
  {% endhighlight %}

  {% highlight java%}
  InterfaceAPI apiService = ServiceGenerator.getClient().create(InterfaceAPI.class);
  // calling
  apiService.listPage(1) // yields /list?page=1
  {% endhighlight %}

* #### API URL : /list?page=1&category=food

  2개의 이상의 Parameter를 보낼때.

{% highlight java %}
// define interface method
@GET("/list")
Call<ResponseBody> listPage(
    @Query("page") int page,
    @Query("category") String category
    //@Query("page") int page2   // 같은 이름도 가능하다. yields /list?page=1&category=food&page=2
);
{% endhighlight %}

{% highlight java%}
InterfaceAPI apiService = ServiceGenerator.getClient().create(InterfaceAPI.class);
// calling
apiService.listPage(1, "food")
{% endhighlight %}

* #### API URL : /list?page=1&page=2 ...

  같은 이름의 Parameter 갯수가 유동적일때

  {% highlight java %}
  // define interface method
  @GET("/list")
  Call<ResponseBody> listPage(
      @Query("page") int... pages  //다른 @Query와 같이 쓸 경우 맨 아래에 위치해야 한다.
  );
  {% endhighlight %}

  {% highlight java%}
  InterfaceAPI apiService = ServiceGenerator.getClient().create(InterfaceAPI.class);
  // calling
  apiService.listPage(1, 2, ...)
  {% endhighlight %}

`@Path`와 마찬가지로 String형일 경우 이미 encode되었는지 여부도 설정할 수 있다. 사용법도 동일.

**Tip!)** Query의 type이 Object형인 경우(ex. String, Integer) **null이 가능**하다. null을 넣으면 /list로 전송 된다.

---

>#### @QueryMap

`@Query`와 특성이 같다.
Query Parameter의 이름과, 갯수를 유동적으로 보낼때 쓰임. 단, 같은 이름으로 보낼 수 없다.

* #### API URL : /list?category=food&kind=instent&orderby=calorie&page=1 ...

{% highlight java %}
// define interface method
@GET("/list")
Call<ResponseBody> listPage (
    @QueryMap Map<String, String> options
);
{% endhighlight %}

{% highlight java%}
// Map 객체 생성
Map<String, String> data = new HashMap<>();

// Map data set
data.put("category", "food");
data.put("search", "instent");
data.put("orderby", "calorie");
data.put("page", "1");
...

InterfaceAPI apiService = ServiceGenerator.getClient().create(InterfaceAPI.class);
// calling
apiService.listPage(data);
{% endhighlight %}

---

>#### @Field 와 @FromUrlEncoded

`@Field`는 HTTP POST/PUT Protocol에서 Parameter를 줄때 쓰인다. 서버의 Field값과 1:1매핑된다. Class형은 넣을 수 없다(넣으면 주소값 들어감). `@Field`를 사용하고자 할 땐, `@FromUrlEncoded`를 필수로 추가해줘야한다. 그렇지 않으면 아래와 같은 Error를 만날 수 있다.

    java.lang.IllegalArgumentException: @Field parameters can only be used with form encoding. (parameter #2)

* #### Parameter : {'title': 'hi', 'subTitle': 'hello'}

  {% highlight java %}
  // define interface method
  @FormUrlEncoded
  @POST("/list")
  Call<ResponseBody> list (
     @Field("title") String title,
     @Field("subTitle") String subTitle
     //@Fields("title") String title2   // 같은 이름도 가능하다. yields {'title': ['hi', '안녕'], 'subTitle': ['hello']}
  );
  {% endhighlight %}

  {% highlight java%}
  InterfaceAPI apiService = ServiceGenerator.getClient().create(InterfaceAPI.class);
  // calling
  apiService.list("hi", "hello");  // yields {'title': ['hi'], 'subTitle': ['hello']}
  {% endhighlight %}

* #### Parameter : {'title': [hi, hello, 안녕, ...]}

  같은 이름의 Parameter 갯수가 유동적일때

  {% highlight java %}
  // define interface method
  @FormUrlEncoded
  @POST("/list")
  Call<ResponseBody> list (
      @Field("title") String... title //다른 @Field 같이 쓸 경우 맨 아래에 위치해야 한다.
  );
  {% endhighlight %}

  {% highlight java%}
  InterfaceAPI apiService = ServiceGenerator.getClient().create(InterfaceAPI.class);
  // calling
  apiService.list("hi", "hello", "안녕");
  {% endhighlight %}

---

>#### @FieldMap

`@Field`와 특성이 같다.
Parameter의 이름과, 갯수를 유동적으로 보낼때 쓰임. 단, 같은 이름으로 보낼 수 없다.

* #### Parameter : {'title': 'hi', 'subTitle': 'hello', 'subTitle2': '안녕'}

{% highlight java %}
// define interface method
@GET("/list")
Call<ResponseBody> list (
    @QueryMap Map<String, Object> options
);
{% endhighlight %}

{% highlight java%}
// Map 객체 생성
Map<String, Object> data = new HashMap<>();

// Map data set
data.put("title", "hi");
data.put("subTitle", "hello");
data.put("subTitle2", "안녕");
...

InterfaceAPI apiService = ServiceGenerator.getClient().create(InterfaceAPI.class);
// calling
apiService.listPage(data);
{% endhighlight %}

---

>#### @Body

`@Body`는 `@Field`와 같이 HTTP POST/PUT Protocol에서 Parameter를 줄때 쓰인다.

...
