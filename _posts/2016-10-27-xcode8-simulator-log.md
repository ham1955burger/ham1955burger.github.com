---
layout: post
title:  "Xcode 8 Simulator Log 없애기"
date:   2016-10-27 12:04:00 +09
categories: Study
---

Xcode 8로 Update 한 뒤로, Simulator에서 아래와 같은 이상한 log들이 뜨기 시작했다. ~~안드로이드인 줄~~

    2016-10-27 13:55:02.627331 StoryboardReferenceTest[26108:5326739] subsystem: com.apple.UIKit, category: HIDEventFiltered, enable_level: 0, persist_level: 0, default_ttl: 0, info_ttl: 0, debug_ttl: 0, generate_symptoms: 0, enable_oversize: 1, privacy_setting: 2, enable_private_data: 0
    2016-10-27 13:55:02.631051 StoryboardReferenceTest[26108:5326739] subsystem: com.apple.UIKit, category: HIDEventIncoming, enable_level: 0, persist_level: 0, default_ttl: 0, info_ttl: 0, debug_ttl: 0, generate_symptoms: 0, enable_oversize: 1, privacy_setting: 2, enable_private_data: 0
    2016-10-27 13:55:02.645845 StoryboardReferenceTest[26108:5326727] subsystem: com.apple.BaseBoard, category: MachPort, enable_level: 1, persist_level: 0, default_ttl: 0, info_ttl: 0, debug_ttl: 0, generate_symptoms: 0, enable_oversize: 0, privacy_setting: 0, enable_private_data: 0
    2016-10-27 13:55:02.662661 StoryboardReferenceTest[26108:5326664] subsystem: com.apple.UIKit, category: StatusBar, enable_level: 0, persist_level: 0, default_ttl: 0, info_ttl: 0, debug_ttl: 0, generate_symptoms: 0, enable_oversize: 1, privacy_setting: 2, enable_private_data: 0
    2016-10-27 13:55:02.779432 StoryboardReferenceTest[26108:5326664] subsystem: com.apple.BackBoardServices.fence, category: App, enable_level: 1, persist_level: 0, default_ttl: 0, info_ttl: 0, debug_ttl: 0, generate_symptoms: 0, enable_oversize: 0, privacy_setting: 0, enable_private_data: 0

이 로그들이 무얼 뜻하는지 찾아보았는데, 아래와 같은 답을 얻을 수 있었다.

    This is a known issue with Beta 4.  Per the release notes:
    Xcode Debug Console shows extra logging from system frameworks when debugging applications in the Simulator. (27331147, 26652255)

참고) iOS 10 버전의 Simulator에서 나타나는 현상이고 그 전 버전에선 나타나지 않았다.

---

<h4> Xcode 8 Debug Console 불필요한 Log 지우기 </h4>

상단 Toolbar에 device선택하는 부분 클릭 ~~...정확한 명칭을 모르겠다(쿨럭)~~

![edit_scheme](/assets/images/xcode8_simulator_log/edit_scheme.png)

Edit Scheme 선택

![edit_scheme2](/assets/images/xcode8_simulator_log/edit_scheme2.png)

Run > Arguments > Environment Variables > OS_ACTIVITY_MODE disable 추가

![edit_scheme3](/assets/images/xcode8_simulator_log/edit_scheme3.png)

이젠 Simulator를 돌려도 이 불필요한 Log가 더이상 보이지 않는다!
