---
layout: post
title:  "Disqus를 이용해 댓글 기능 추가하기"
date:   2016-10-19 10:22:00 +09
categories: Study
---
<h4> Disqus </h4>
: Website에 댓글 기능을 추가 할 수 있는 무료 Comment Tool


Github 뿐만 아니라 wordPress, Blogger, ... 등 여러 platform에서 사용 할 수 있다.

댓글이 달리면 mail로 알림이 간다.

Disqus 회원이 아니더라도 댓글을 달 수 있고, Twitter와 Facebook에 공유도 가능하다.

---

<h4> 사용준비 </h4>

* Disqus 회원가입

  [Disqus 공식 홈페이지](https://disqus.com/home/explore/)

* Disqus에서 가입한 email로 verify메일 확인

* Disqus 회원 정보 수정

  * 로그인 후 > 우측 상단 > View Profile 선택 > Edit Profile 선택

  ![disqus_view_profile](/assets/images/disqus/disqus_view_profile.png)

  * 알맞은 정보 입력 후 Save

  ![disqus_edit_profile2](/assets/images/disqus/disqus_edit_profile.png)

* Disqus 설정

  * 우측 상단 > Add Disqus To Site 선택

  ![disqus_add_disqus_to_site](/assets/images/disqus/disqus_add_disqus_to_site.png)

  * 최 하단 > GET STARTED 선택

  ![disqus_add_disqus_to_site2](/assets/images/disqus/disqus_add_disqus_to_site2.png)

  * I want to install Disqus on my site 선택

  ![disqus_add_disqus_to_site3](/assets/images/disqus/disqus_add_disqus_to_site3.png)

  * 알맞은 정보 입력 후 Create Site

  ![disqus_add_disqus_to_site4](/assets/images/disqus/disqus_add_disqus_to_site4.png)

  * Let's get started!

  ![disqus_add_disqus_to_site5](/assets/images/disqus/disqus_add_disqus_to_site5.png)

---
<h4> Github 블로그에 Disqus 적용하기 </h4>

  * platform 선택

    Github이 list에 없으므로 Universal Code 선택

  ![disqus_choose_platform](/assets/images/disqus/disqus_choose_platform.png)

  * Universal Code Copy

    1번 전체 복사

  ![disqus_universal_code](/assets/images/disqus/disqus_universal_code.png)

  * Post Template에 적용

        $ vi /Users/user/Documents/Dev/jekyll/ham1955burger.github.com/_layouts/post.html

    {% highlight html %}
    <! -- ham1955burger.github.com/_layouts/post.html -->

    <! -- set disques -->
    <div id="disqus_thread"></div>
    <script>
      /**
      *  RECOMMENDED CONFIGURATION VARIABLES: EDIT AND UNCOMMENT THE SECTION BELOW TO INSERT DYNAMIC VALUES FROM YOUR PLATFORM OR CMS.
      *  LEARN WHY DEFINING THESE VARIABLES IS IMPORTANT: https://disqus.com/admin/universalcode/#configuration-variables*/
      /*
      var disqus_config = function () {
      this.page.url = PAGE_URL;  // Replace PAGE_URL with your page's canonical URL variable
      this.page.identifier = PAGE_IDENTIFIER; // Replace PAGE_IDENTIFIER with your page's unique identifier variable
      };
      */
      (function() { // DON'T EDIT BELOW THIS LINE
        var d = document, s = d.createElement('script');
        s.src = '//ham1955burger.disqus.com/embed.js';
        s.setAttribute('data-timestamp', +new Date());
        (d.head || d.body).appendChild(s);
      })();
    </script>
    <noscript>Please enable JavaScript to view the <a href="https://disqus.com/?ref_noscript">comments powered by Disqus.</a></noscript>
    <! -- END set disques -->
    {% endhighlight %}

  ---

  <h4> 마무리 </h4>

  *이젠 아래와 같이 블로그에서 댓글기능을 사용 할 수 있다!*

  ![disqus_comment_form](/assets/images/disqus/disqus_comment_form.png)

  ---

  <h4> 추가 </h4>

  * *Disqus 회원이 아닌 사람들도 comment 작성이 가능하게 설정하기*

    ~~스팸봇의 테러 위험으로 잘 사용하지 않는다고 한다(?)~~

    Disqus Home -> 우측 상단 Admin -> Settings -> Community

    Guest Commenting 항목 체크

    ![disqus_set_guest_comment](/assets/images/disqus/disqus_set_guest_comment.png)

  Guest일 경우 Name, Email, Password 입력 후 I'd rather post as a guest까지 체크해야 comment를 작성 할 수 있다.

  ![disqus_guest_comment](/assets/images/disqus/disqus_guest_comment.png)
