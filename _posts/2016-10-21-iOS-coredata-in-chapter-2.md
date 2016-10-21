---
layout: post
title:  "iOS Core Data in Swift #2"
date:   2016-10-21 14:26:00 +09
categories: Study
published: false
---
<h4> Core Data Framework </h4>
: 앱 내부의 Database(sqlite3)를 SQL없이 접근 가능 하게 해주는(wrapping) Framework

---
<h4> 목표 </h4>

: CoreData를 이용해 Database Table을 만들어 보고, Record를 Insert, Update, Delete 해보자.


: 방이 있는 채팅창 만들기

*아직 CoreData에 대해 잘 알지 못하여 무작정 따라해 보는 것으로 정리한다. 추후 깨달음이 있으면 수정하는걸로..*

---

* [#1 Core Data 설정하기](https://ham1955burger.github.io/study/2016/10/20/iOS-coredata-in-chapter-1.html)

* <h4> #2 Core Data를 이용해 Record 다뤄보기 </h4>

---

<h4> Insert </h4>

* Room Entity에 Record 추가하기

  채팅방을 만들어 보자.

  {% highlight swift %}

    // get Room entity
    let roomDescription: NSEntityDescription = NSEntityDescription.entity(forEntityName: "Room", in: appDelegate.managedObjectContext!)!

    // ORM 형식으로 set record
    let roomRecord = Room(entity: roomDescription, insertInto: appDelegate.managedObjectContext)
    roomRecord.updateDate = NSDate()
    roomRecord.name = "Chat room test"

    do {
      // record save
      try roomRecord.managedObjectContext?.save()
    } catch {
      // save error
      print("Error with request: \(error)")
    }

  {% endhighlight %}

  CoreDataTest.sqlite 파일을 열어 Room Entity에 Record가 insert되었는지 확인

  ![coredata_create_project](/assets/images/coreData_chapter_2/coredata_insert_room.png)
