---
layout: post
title:  "Android Retrofit2 사용하기 - GET"
date:   2017-01-11 18:27:00 +09
categories: Android
---

이번 글에선 Retrofit GET 사용법에 대해 좀 더 알아보도록 하겠다.

---

#### 연관글

* 이어진 글) [Android HTTP Library - Retrofit2](https://ham1955burger.github.io/android/2017/01/09/Android-HTTP-Library-Retrofit2.html)

* ##### Android Retrofit2 사용하기 - GET

---

>#### GET Parameter

이어진 글에서 Retrofit을 이용해 간단한 GET 방식을 사용해 보았다.

API URL에 Parameter를 주어 좀 더 복잡한 형태로 만들어보자.

Retrofit에서 GET Parameter를 주는 방법은 3가지가 있다.

* @Path

* @Query

* @QueryMap

---

>#### @Path를 이용한 GET Parameter

* ##### API URL : list/1

    보통 리스트 중 1개 항목을 선택하여 상세정보를 요청할 때, 선택한 항목의 {pk}값을 Parameter로 서버에 전송한다.

  {% highlight java %}
  // define interface method
  @GET("/list/{pk}")
  Call<ResponseBody> listDetail(@Path("pk") int pk);
  {% endhighlight %}

  {% highlight java%}
  // calling
  apiService.listDetail(1)
  {% endhighlight %}

* ##### API URL : list/ham+1955+burger

  client에서 String으로 파라미터를 보낼 경우, 이상한 문자가 섞여서 서버로 전송되는 경우가 있다.
  예를 들어, **instent+food 전송했으나 instent%2Bfood로 전송**되는 경우.

  문제는 URL Encoding때문인데, Retrofit @Path엔 이미 encode되었는지 여부를 설정하여 서버에 전송할 수 있다. (default는 false)

++ **encoded 되어있지 않은 경우**

  {% highlight java %}
  // define interface method
  @GET("/list/{category}")
  Call<ResponseBody> categoryNotAlreadyEncoded(
      @Path("category") String category
  );
  {% endhighlight %}

  {% highlight java%}
  // calling
  apiService.categoryNotAlreadyEncoded("instent+food") // yields /list/instent%2Bfood
  {% endhighlight %}

++ **이미 encoded 된 경우**

  {% highlight java %}
  // define interface method
  @GET("/list/{category}")
  Call<ResponseBody> categoryAlreadyEncoded(
      @Path(value="category", encoded=true) String category
  );
  {% endhighlight %}

  {% highlight java%}
  // calling
  apiService.categoryAlreadyEncoded("instent+food") // yields /list/instent+food
  {% endhighlight %}

//TODO: TEST - 안될 것 같지만, Integer일 경우 null넣어서.

**Tip!)** Path의 type이 String인 경우 **""**도 가능하다(null은 불가능).
서버에서 **/list** 와 **/list/** 모두 지원할때 쓸만하다.
혹, URL이 **/list/{category}/kind** 일 경우, ""을 쓰게되면 **/list//kind**가 되므로 쓸 수 없다.


---

>#### @Query를 이용한 GET Parameter

  URL에 Query Parameter를 추가할때 쓰인다. @Path처럼 /list/1 꼴이 아닌 **/list?page=1** 꼴이다.

* ##### API URL : /list?page=1

  {% highlight java %}
  // define interface method
  @GET("/list")
  Call<ResponseBody> listPage(
      @Query("page") int page
  );
  {% endhighlight %}

  {% highlight java%}
  // calling
  apiService.listPage(1) // yields /list?page=1
  {% endhighlight %}

* ##### API URL : /list?page=1&category=food

  다른이름의 파라미터

{% highlight java %}
// define interface method
@GET("/list")
Call<ResponseBody> listPage(
    @Query("page") int page
    @Query("category") String category
);
{% endhighlight %}

{% highlight java%}
// calling
apiService.listPage(1, "food")
{% endhighlight %}

* ##### API URL : /list?page=1&page=2

  같은 이름의 파라미터

  {% highlight java %}
  // define interface method
  @GET("/list")
  Call<ResponseBody> listPage(
      @Query("page") int... pages
  );
  {% endhighlight %}

  {% highlight java%}
  // calling
  apiService.listPage(1, 2)
  {% endhighlight %}

//TODO: TEST -> @Query("page") int page, @Query("page") int page2 이거 되는지 확인하고 되면 추가할것.

@Path와 마찬가지로 String형일 경우 이미 encode되었는지 여부도 설정할 수 있다. 사용법도 동일.

//TODO: TEST 다른 자료형들 또는 object형들.

**Tip!)** Query의 type이 String, Integer인 경우 **null**도 가능하다. null을 넣으면 /list로 전송 된다.

---

>#### @QueryMap을 이용한 GET Parameter

Query Parameter의 이름과, 갯수를 유동적으로 보낼때 유용.

// TODO: TEST 아래코드
// TODO: Map data set할때 같은이름으로 들어가는지 확인


* ##### API URL : /list?category=food&kind=instent&orderby=calorie&page=1 ...

{% highlight java %}
// define interface method
@GET("/list")
Call<ResponseBody> listPage(
    @QueryMap( Map<String, String> options)
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

// calling
apiService.listPage(data);
{% endhighlight %}
