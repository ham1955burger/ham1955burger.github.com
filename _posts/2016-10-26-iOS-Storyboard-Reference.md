---
layout: post
title:  "iOS Storyboard Reference 사용하기"
date:   2016-10-26 16:04:00 +09
categories: Study
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

오늘은 Storyboard의 속도 개선을 위해 한 파일의 Storyboard를 쪼개는 방법을 정리하고자 한다.(merge conflict을 낮추는 방안도 될 수 있다.)

~~사실 너무 쉬워서 posting 안하려고 했는데 나의 머리를 믿을 수 있어야지(...)~~

---

<h4> Storyboard가 이미 구성되어 있는 상태에서 Storyboard 쪼개기 </h4>

  현재 Main.storyboard는 아래와 같이 구성되어있다.

  ![main_storyboard](/assets/images/storyboard_reference/main_storyboard.png)

  쪼갤 ViewController 영역을 드래그 drag 하여 선택

  ![main_storyboard_drag](/assets/images/storyboard_reference/main_storyboard_drag.png)

  또는 Command + click 하여 선택 상태로 만들어 줌(파란색 테두리)

  ![main_storyboard_select](/assets/images/storyboard_reference/main_storyboard_select.png)

  Xcode navigation bar > Editor > Refactor to Storyboard... 선택

  ![refactor_to_storyboard](/assets/images/storyboard_reference/refactor_to_storyboard.png)

  자동으로 Storyboard 파일이 생기고, 내가 선택했던 ViewController들이 그 안으로 옮겨졌다.

  Main.storyboard - MainViewController와 연결되어있던 AViewController가 Initial View Controller로 자동 설정

  ![sub_storyboard](/assets/images/storyboard_reference/sub_storyboard.png)

  Main.storyboard를 보면, 아래와 같이 Storyboard Reference가 자동 생성 된 것을 확인 할 수 있다.

  ![refactor_to_storyboard_success](/assets/images/storyboard_reference/refactor_to_storyboard_success.png)

---

<h4> Storyboard에 새로운 Storyboard를 연결하고자 할때 </h4>

  Main.storyboard - MainViewController의 `go to B` Button에 storyboard reference를 연결해보자.

  Test.storyboard를 만들고 storyboard가 시작할 ViewController 선택 후 우측 설정창 > View Controller > Is Initial View Controller 체크

  ![test_storyboard](/assets/images/storyboard_reference/test_storyboard.png)

  Main.storyboard - MainViewcontroller의 `go to B` Button에 Test.storyboard를 연결하게 위해 `Storyboard Reference` 끌어다 놓음

  ![storyboard_reference](/assets/images/storyboard_reference/storyboard_reference.png)

  Storyboard Reference 설정창 > 연결될 Storyboard 파일 설정

  ![connect_reference](/assets/images/storyboard_reference/connect_reference.png)

  `go to B` Button과 연결

  ![connect_reference2](/assets/images/storyboard_reference/connect_reference2.png)

  `go to B` Button을 누르면, Test.storyboard의 Initial View Controller로 연결 되는 것을 확인 할 수 있다.

---

<h4> Initial View Controller가 아닌 다른 View Controller로 연결 하고 싶어요! </h4>

  안될리가 없지

  해당 ViewController에 이름을 지정

  ![connect_reference3](/assets/images/storyboard_reference/connect_reference3.png)

  Storyboard Reference의 Referenced ID에 지정한 이름을 적어주면 된다.

  ![connect_reference4](/assets/images/storyboard_reference/connect_reference4.png)


---
<h4> 읽어보기 </h4>
[Xib(Nib)에 대해 설명 잘되어있는 글](http://soooprmx.com/wp/archives/4299)

[Storyboard vs. NIB vs. Code](https://www.toptal.com/ios/ios-user-interfaces-storyboards-vs-nibs-vs-custom-code)
