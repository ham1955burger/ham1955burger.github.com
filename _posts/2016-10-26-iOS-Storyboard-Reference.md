---
layout: post
title:  "iOS Storyboard Reference 사용하기"
date:   2016-10-26 16:04:00 +09
categories: Study
published: false
---

처음 iOS를 접했을때 Storyboard를 이용하였고, 지금은 Xib를 이용하고 있다.

둘다 써보면서 느낀점을 정리하면,

<h4> Storyboard </h4>

* 장점

  1) ViewController간에 흐름을 설정 할 수 있고, 볼 수 있으므로 쉽게 파악 가능

  2) TableView를 static으로 구현 가능

  3) TableViewCell을 TableView 안에 만들 수 있음(CollectionViewCell도 마찬가지)

* 단점

  1) Stroyboard 파일의 규모가 커지면 속도가 현저히 저하. 무겁다.

  2) 재사용 불가능(장점의 3번도 이에 속함)

<h4> Xib(Nib) </h4>

* 장점

  1) 재사용 가능

* 단점

  흠.. 굳이 쓰자면 ViewController간의 흐름을 코드로 설정하고, 확인해야 한다는 점(?)

2인 이상 만들 경우, merge conflict의 빈도와 규모는 Storyboard > Xib ~~(생각하고 싶지 않음)~~

**사실 다른건 모르겠고 Storyboard는 한눈에 볼 수 있어서 좋다. 이쁨(?)**

---

<h4> Storyboard 쪼개기 </h4>

앞서 말했듯이 Storyboard는 파일의 규모가 커지면 속도가 현저히 저하된다.

이 방안으로 Storyboard를 한 파일에서 만들지 않고 쪼갤 수 있다.(merge conflict을 낮추는 방안도 될 수 있다.)

---
<h4> 읽어보기 </h4>
[Xib(Nib)에 대해 설명 잘되어있는 글](http://soooprmx.com/wp/archives/4299)

[Storyboard vs. NIB vs. Code](https://www.toptal.com/ios/ios-user-interfaces-storyboards-vs-nibs-vs-custom-code)
