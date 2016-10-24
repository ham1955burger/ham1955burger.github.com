---
layout: post
title:  "iOS Core Data in Swift #1"
date:   2016-10-20 10:16:00 +09
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

* <h4> #1 Core Data 설정하기 </h4>


* [#2 Core Data를 이용해 Record 다뤄보기](https://ham1955burger.github.io/study/2016/10/20/iOS-coredata-in-chapter-2.html)

---

<h4> Project 생성 </h4>

* Use Core Data 선택 후 생성

  ![coredata_create_project](/assets/images/coreData_chapter_1/coredata_create_project.png)

  기존 프로젝트에선 Xcode navigation bar > File > New > File > Core Data > Data Model 로 생성 가능

* Project 구조를 보면, `CoreDataTest.xcdatamodeld` 파일을 볼 수 있다.

  ![coredata_tree](/assets/images/coreData_chapter_1/coredata_tree.png)

* `AppDelegate.swift`에 아래와 같은 코드가 자동으로 추가 된 것을 볼 수 있다.

  *자동 추가 되는 이유가 있을텐데, 현재 이 게시글은 아래 코드를 사용하지 않는다(...)*

  ![coredata_appdelegate_auto_added_code](/assets/images/coreData_chapter_1/coredata_appdelegate_auto_added_code.png)

---
<h4> Database Schema 만들기 </h4>

CoreData에서는 Table은 `Entity`, Field는 `Attribute`, ForeignKey는 `Relationship`으로 정의되어 있다.

`Fetched Property`는 SQL문법에서 WHERE 구문이라고 생각하면 된다.

|     DB     |      CoreData     |
|:----------:|:-----------------:|
|    Table   |       Entity      |
|:----------:|:-----------------:|
|    Field   |      Attribute    |
|:----------:|:-----------------:|
| ForeignKey |    Relationship   |
|:----------:|:-----------------:|
|    WHERE   |  Fetched Property |

* **Schema**


  ![coredata_schema](/assets/images/coreData_chapter_1/coredata_schema.png)


* **Entity 만들기**

  CoreDataTest.xcdatamodeld 선택 후 Add Entity > Entity 이름 변경 > Attributes 추가

  * Room

  ![coredata_create_schema](/assets/images/coreData_chapter_1/coredata_create_schema.png)

  * Chat

  ![coredata_create_schema2](/assets/images/coreData_chapter_1/coredata_create_schema2.png)

  Editor Style을 변경하면 Graph 형식으로 볼 수도 있다!

  ![coredata_editor_style](/assets/images/coreData_chapter_1/coredata_editor_style.png)

* **Relationship 설정하기**

  * Room

  ![coredata_relationship_room](/assets/images/coreData_chapter_1/coredata_relationship_room.png)

  * Chat

  ![coredata_relationship_chat](/assets/images/coreData_chapter_1/coredata_relationship_chat.png)

  선이 생겼다! 뭔가 이어진듯한 느낌!

  화살표는 Entity사이의 관계를 나타내는데 Default로 To One type으로 설정된다.

  ![coredata_editor_style_relationship](/assets/images/coreData_chapter_1/coredata_editor_style_relationship.png)

  Room과 Chat의 관계는 1:N 관계이므로 수정하자.

  Room에 설정한 Relationship(chat)을 선택 후 우측 설정 창에서 Type `To Many`로 수정,

  Room이 삭제되었을 경우, Room에 연결된 Chat들을 자동 삭제해주기 위해 Delete Rule도 `Cascade`로 수정하였다.

  *나는 어느 Relationship에서 Type 설정을 해줘야하는지 좀 헷갈렸다. 사실 N:1관계로 설정해 놓고 삽질을 하기도 했다(...)*

  ![coredata_relationship_setting](/assets/images/coreData_chapter_1/coredata_relationship_setting.png)

  Graph Style로 봤을때, Room과 Chat의 관계에 화살표가 2개가 생긴것으로 To Many type으로 바뀐 것을 확인 할 수 있다.

  ![coredata_editor_style_relationship2](/assets/images/coreData_chapter_1/coredata_editor_style_relationship2.png)

* **Fetched Properties 설정하기**

  *얘는 자료를 못찾았다.. 현재 코드에서 설정해주었는데 이는 추후 다른 chapter에서 다루도록 하겠다.*

* **ManagedObject Subclass 생성**

  Xcode Navigation bar > Edit > Create NSManagedObject Subclass... 선택

  ![coredata_create_manage_object_subclass](/assets/images/coreData_chapter_1/coredata_create_manage_object_subclass.png)

  아래와 같은 파일들이 자동 생성 되었다!

  ~properties.swift 파일을 열어보면 내가 구성한 Attribute들로 변수들이 생성된 것을 볼 수 있다.

  추후 이 변수들로 database에 접근 할 수 있다. (ORM의 개념이라고 보면 되고, 파일이 없어도 직접 접근의 방식으로 접근할 수도 있다.)

  ![coredata_create_manage_object_subclass2](/assets/images/coreData_chapter_1/coredata_create_manage_object_subclass2.png)

---
<h4> Core Data Stack </h4>
  * Core Data를 쓰기 위해 AppDelegate.swift에 아래와 같은 코드를 추가

  {% highlight swift %}
  // AppDelegate.swift

  import CoreData

  ...

  // MARK: - Core Data Stack

  lazy var applicationDocumentsDirectory: NSURL = {
      // The directory the application uses to store the Core Data store file. This code uses a directory named "com.jqsoftware.MyLog" in the application's documents Application Support directory.
      let urls = FileManager.default.urls(for: .documentDirectory, in: .userDomainMask)
      return urls[urls.count-1] as NSURL
  }()

  lazy var managedObjectModel: NSManagedObjectModel = {
      // momd : the managed object model uses to create the application's data model
      let modelURL = Bundle.main.url(forResource: "CoreDataTest", withExtension: "momd")!
      return NSManagedObjectModel(contentsOf: modelURL)!
  }()

  lazy var persistentStoreCoordinator: NSPersistentStoreCoordinator? = {
      // The persistent store coordinator for the application. This implementation creates and return a coordinator, having added the store for the application to it. This property is optional since there are legitimate error conditions that could cause the creation of the store to fail.
      // Create the coordinator and store
      var coordinator: NSPersistentStoreCoordinator? = NSPersistentStoreCoordinator(managedObjectModel: self.managedObjectModel)
      let url = self.applicationDocumentsDirectory.appendingPathComponent("CoreDataTest.sqlite")

      // CoreDataTest.sqlite file path
      print(url)

      var error: NSError? = nil
      var failureReason = "There was an error creating or loading the application's saved data."

      do
      {
          try coordinator!.addPersistentStore(ofType: NSSQLiteStoreType, configurationName: nil, at: url, options: nil)
      } catch let error as NSError {
          coordinator = nil
          var dict = [String: AnyObject]()
          dict[NSLocalizedDescriptionKey] = "Failed to initialize the application's saved data" as AnyObject?
          dict[NSLocalizedFailureReasonErrorKey] = failureReason as AnyObject?
          dict[NSUnderlyingErrorKey] = error
          //            error = NSError(domain: "YOUR_ERROR_DOMAIN", code: 9999, userInfo: dict)
          // Replace this with code to handle the error appropriately.
          // abort() causes the application to generate a crash log and terminate. You should not use this function in a shipping application, although it may be useful during development.
          NSLog("Unresolved error \(error), \(error.userInfo)")
          abort()
      }

      return coordinator
  }()

  lazy var managedObjectContext: NSManagedObjectContext? = {
      // Returns the managed object context for the application (which is already bound to the persistent store coordinator for the application.) This property is optional since there are legitimate error conditions that could cause the creation of the context to fail.
      let coordinator = self.persistentStoreCoordinator
      if coordinator == nil {
          //TODO: 예외처리?
          return nil
      }
      var managedObjectContext = NSManagedObjectContext(concurrencyType: NSManagedObjectContextConcurrencyType.mainQueueConcurrencyType)
      managedObjectContext.persistentStoreCoordinator = coordinator
      return managedObjectContext
  }()
  {% endhighlight %}

---

<h4> 근데 CoreDataTest.sqlite 파일은 어디있지? </h4>

*분명 설정은 다 했는데! build successed 후 앱도 띄웠는데!! Database가 잘 만들어졌나 확인 해보고싶은데!!! Project폴더를 아무리 찾아봐도 .sqlite파일이 없다!!!!*

.sqlite파일은 Application Documents Directory에 숨어있다.

방금 AppDelegate.swift에 코드를 추가한 부분을 다시한번 살펴보자.

persistentStoreCoordinator 부분, CoreDataTest.sqlite의 file path를 확인하기 위해 log를 찍어보았다.

    Optional(file:///Users/user/Library/Developer/CoreSimulator/Devices/6C38CD42-5BAD-4F01-A408-AB84A943B3AA/data/Containers/Data/Application/962E06FB-2848-408B-8260-CA0A50B2DF88/Documents/CoreDataTest.sqlite)

App Store에서 `Datum Free`라는 sqlite database viewer를 설치하여 CoreDataTest.sqlite를 열어보았다.

내가 만든 Entity와 Attribute 대로 DB가 만들어진 것을 확인 할 수 있다.

![coredata_open_sqlite](/assets/images/coreData_chapter_1/coredata_open_sqlite.png)


---

<h4> 마무리 </h4>

너무 길어져서 Insert, Select, Update, Delete는 #2에서 진행하도록 한다.

---
<h4> 참고 </h4>

[Apple Developer Core Data](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/CoreData/index.html)

[core-data-and-swift by Bart Jacobs](https://code.tutsplus.com/series/core-data-and-swift--cms-907)
