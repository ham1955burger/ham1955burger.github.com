---
layout: post
title:  "Android Studio Constraint 사용하기"
date:   2016-12-29 11:23:00 +09
categories: Android
---

[Android Developer ConstraintLayout 바로가기]("https://developer.android.com/training/constraint-layout/index.html#add-a-constraint")

Android는 `XML`을 이용해 화면 구성을 한다.

iOS의 화면 구성처럼 띡띡 갖다 붙이는게 아닌, text형식으로 화면을 구성하려니 익숙하지도 않고..

~~(Android 해봐야지 하면서도 멀리했던게 화면 구성이었던거 같기도 하고.. 막상 하면 아무것도 아닌데..)~~

Android Studio 2.2 버전부터 Constraint 를 지원한다고 하여 Study 할 겸 사용해 보았다.

---

<h4> Project에 ConstraintLayout 추가하기 </h4>

1) navigation bar > Tools > Android > SDK Manager

![add_constraint_1](/assets/images/android_studio_constraint/add_constraint_1.png)

2) Android SDK > SDK Tools > Support Repository > `ConstraintLayout for Android`, `Solver for constraintLayout` 체크

![add_constraint_2](/assets/images/android_studio_constraint/add_constraint_2.png)

3) app 내에 있는 `build.gradle` 파일 클릭

![add_constraint_3](/assets/images/android_studio_constraint/add_constraint_3.png)

4) build.gradle 파일의 dependencies 에 `compile 'com.android.support.constraint:constraint-layout:1.0.0-beta4'` 추가

![add_constraint_4](/assets/images/android_studio_constraint/add_constraint_4.png)

준비 완료.

---

<h4> ConstraintLayout 사용하기 </h4>

ConstraintLayout을 만드는 방법은 3가지가 있다.

1) 기존 Layout을 ConstraintLayout 으로 변경하기

  ![make_constraint_1](/assets/images/android_studio_constraint/make_constraint_1.png)

2) XML의 Root Tag에 ConstraintLayout 명시해주기

  ![make_constraint_2](/assets/images/android_studio_constraint/make_constraint_2.png)

3) XML 파일을 만들때 ConstraintLayout 으로 만들기  

  ![make_constraint_3_1](/assets/images/android_studio_constraint/make_constraint_3_1.png)

  ![make_constraint_3_2](/assets/images/android_studio_constraint/make_constraint_3_2.png)

---

<h4> ConstraintLayout을 이용해 화면 구성하기 </h4>

해보면 앎!

---

<h4> 느낀점 </h4>

기대를 많이 한 탓일까, iOS에 익숙해진 탓일까, 아직 skill이 부족한 것일까. ~~(흠)~~

LinearLayout 밖에 모르는 나로썬 **나름 만족**.

Android는 기기별로 해상도가 달라서 Resizing을 해줘야 한다는데,

이 부분은 Constraint로도 보완이 안될 것 같다. ~~(당연한 건가?)~~

iOS와 비슷하나.. 기능이라던지, 사용감이라던지. 음.. 아직 Beta 니깐 점점 나아지겠지. ~~(+ 내 skill도)~~

iOS를 먼저 경험한 Android 뉴비로써 없는 것보단 낫다!

현재 혼용해서 작업중. 이상무!

---

<h4> 추가 </h4>

사용하면서 겪었던 문제

* layout_width, layout_height 가 자꾸 0dp로 reset이 돼요.

  나 같은경우는 layout_width를 match_parent로 잡았었는데, 작업하던 파일을 끄고 다시 불러올때 layout_width와 layout_height가 자꾸 0dp로 reset되어 배치가 이상해졌었다.
  버그인가 했는데, developer site에 가보니
  ConstraintLayout은 match_parent 를 사용하지 않는다고 한다.
  그대신 AnySize로 0dp주면 match_parent처럼 잡힘.

* ImageView에 Image 가 보이지 않아요.

  ImageView를 가져다 쓰면 자동으로 이미지를 지정하는 창이 뜬다. 여기서 이미지를 지정해주면 SrcCompat으로 잡히는데, Backgroud로 바꿔줘야 화면에 표시가 된다.

* Rendering problems

  드문 경우 같은데, Rendering problems ~~ refresh 해주세요. 라고 error가 뜨면서,
  화면 preview에 component 배치도 다 틀어졌었다. 아래와 같이 해결.

  navigation bar > File > Invalidate Caches / Restart ... > Just restart

  ![android_rendering_problems](/assets/images/android_studio_constraint/android_rendering_problems.png)
