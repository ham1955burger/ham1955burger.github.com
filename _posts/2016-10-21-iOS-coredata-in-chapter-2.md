---
layout: post
title:  "iOS Core Data in Swift #2"
date:   2016-10-21 14:26:00 +09
categories: Study
---
<h4> Core Data Framework </h4>
: 앱 내부의 Database(sqlite3)를 SQL없이 접근 가능 하게 해주는(wrapping) Framework

---
<h4> 목표 </h4>

: CoreData를 이용해 Database Table을 만들어 보고, Record를 Insert, Select, Update, Delete 해보자.


: 방이 있는 채팅창 만들기

*아직 CoreData에 대해 잘 알지 못하여 무작정 따라해 보는 것으로 정리한다. 추후 깨달음이 있으면 수정하는걸로..*

---

* [#1 Core Data 설정하기](https://ham1955burger.github.io/study/2016/10/20/iOS-coredata-in-chapter-1.html)

* <h4> #2 Core Data를 이용해 Record 다뤄보기 </h4>

---

<h4> Insert </h4>

* Room Entity에 Record 추가하기

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

  ![coredata_insert_room](/assets/images/coreData_chapter_2/coredata_insert_room.png)

* Chat Entity에 Record 추가하기

  {% highlight swift %}

    // get Chat entity
    let chatDescription: NSEntityDescription = NSEntityDescription.entity(forEntityName: "Chat", in: appDelegate.managedObjectContext!)!

    // ORM 형식으로 set record
    let chatRecord = Chat(entity: chatDescription, insertInto: appDelegate.managedObjectContext)
    chatRecord.sendDate = NSDate()
    chatRecord.isReceived = true
    chatRecord.message = "받기 테스트"
    chatRecord.sender = 1
    // Chat과 1:N 관계를 가질 roomObj
    chatRecord.room = self.roomObj

    // room object의 udpateDate를 변경(for room sort)
    self.roomObj?.updateDate = chatRecord.sendDate

    do {
      // record save
      try chatRecord.managedObjectContext?.save()
    } catch {
      // save error
      print("Error with request: \(error)")
    }

  {% endhighlight %}

  CoreDataTest.sqlite 파일을 열어 Room Entity와 1:N관계를 가지는 Chat Record가 insert되었는지 확인

  ![coredata_insert_chat](/assets/images/coreData_chapter_2/coredata_insert_chat.png)

---

<h4> Select & FetchRequest </h4>

CoreData는 `FetchRequest`를 통해 DB에 저장된 값들을 조회 할 수 있다.

* 해당 Room을 ForeignKey로 가지는 Chat을 가져오기

  #1에서 넘어갔던 Fetch Property를 코드로 구현

  {% highlight swift %}

    do {
      // get Chat records
      let fetchRequest: NSFetchRequest<Chat> = Chat.fetchRequest()

      // SELECT * FROM Chat WHERE room = "self.roomObj"
      // 소문자 주의!!!!!!!!!
      // 해당 roomObj를 ForeignKey로 가지는 Chat들만 조회
      let predicate = NSPredicate(format: "room == %@", self.roomObj!)
      fetchRequest.predicate = predicate

      self.result = try appDelegate.managedObjectContext!.fetch(fetchRequest)

      print("-----")
      for record in self.result! as [Chat] {
          print(record.message)
      }
      print("-----")
    } catch {
      print("Error with request: \(error)")
    }

  {% endhighlight %}

  ![coredata_select_chat_record](/assets/images/coreData_chapter_2/coredata_select_chat_record.png)

---

<h4> Update </h4>

* Chat Record 수정하기

  해당 Room의 첫번째 Record의 message를 수정

  {% highlight swift %}

    // 주소값이 저장되나봄?
    self.result![0].message = "받기 수정 테스트"

    do {
      try appDelegate.managedObjectContext?.save()
    } catch {
      print("Error with request: \(error)")
    }

  {% endhighlight %}

  ![coredata_update_chat_record](/assets/images/coreData_chapter_2/coredata_update_chat_record.png)

---

<h4> Delete </h4>

* Chat Record 삭제하기

  해당 Room의 첫번째 Record 삭제

  {% highlight swift %}

    appDelegate.managedObjectContext?.delete(self.result![0])

    do {
      try appDelegate.managedObjectContext?.save()
    } catch {
      print("Error with request: \(error)")
    }

  {% endhighlight %}

  ![coredata_delete_chat_record](/assets/images/coreData_chapter_2/coredata_delete_chat_record.png)

---
<h4> 마무리 </h4>

위와 같은 방법으로 Room Record를 삭제하면, 설정해놓은 cascade 속성으로 인해 해당 Room Record를 FK로 한 Chat Record들이 함께 삭제되는 것을 확인 할 수 있다.

#3 에서는 기존 DB를 Migration하는 방법에 대해 진행하도록 하겠다.


---
