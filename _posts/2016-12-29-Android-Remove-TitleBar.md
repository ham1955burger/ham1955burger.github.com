---
layout: post
title:  "Android TitleBar 없애기"
date:   2016-12-29 16:15:00 +09
categories: Android
---

Android 뉴비는 첫 Project를 만들게 되는데... ~~(엇. 안녕 Hello World...!)~~

![TitleBar](/assets/images/android_remove_actionbar/TitleBar.png)

---

#### Android TitleBar 없애기

* AndroidManifest.xml 수정하기

    `android:theme="@style/Theme.AppCompat.Light.NoActionBar"`

![AndroidManifest](/assets/images/android_remove_actionbar/AndroidManifest.png)

상단 TitleBar가 사라진 것을 볼 수 있다.

![Removed_TitleBar](/assets/images/android_remove_actionbar/Removed_TitleBar.png)

`android:theme="@style/Theme.AppCompat.NoActionBar"` 로 적용하면 ~~까망까망해 ㅎㅎㅎ~~

![Removed_TitleBar_Black](/assets/images/android_remove_actionbar/Removed_TitleBar_Black.png)
