---
layout: post
title:  "iOS Core Data Tutorial in Swift"
date:   2016-10-20 10:16:00 +09
categories: Study
published: false
---
<h4> Core Data Framework </h4>
: 앱 내부의 Database(sqlite3)를 SQL없이 접근 가능 하게 해주는(wrapping) Framework

---
<h4> 목표 </h4>

: CoreData를 이용해 Database Table을 만들어 보고, Recored를 Insert, Update, Delete 해보자.


: 방이 있는 채팅창 만들기

*아직 CoreData에 대해 잘 알지 못하여 무작정 따라해 보는 것으로 정리한다. 추후 깨달음이 있으면 수정하는걸로..*

---
<h4> Project 생성 </h4>

* Use Core Data 선택 후 생성

  ![coredata_create_project](/assets/images/coreData/coredata_create_project.png)

  기존 프로젝트에선 Xcode navigation bar > File > New > File > Core Data > Data Model 로 생성 가능

* Project 구조를 보면, `CoreDataTest.xcdatamodeld` 파일을 볼 수 있다.

  ![coredata_tree](/assets/images/coreData/coredata_tree.png)

* `AppDelegate.swift`에 아래와 같은 코드가 자동으로 추가 된 것을 볼 수 있다.

  *자동 추가 되는 이유가 있을텐데, 현재 이 게시글은 아래 코드를 사용하지 않는다(...)*

  ![coredata_appdelegate_auto_added_code](/assets/images/coreData/coredata_appdelegate_auto_added_code.png)

---
<h4> Database Schema 만들기 </h4>

CoreData에서는 Table은 `Entity`, Field는 `Attribute`, ForeignKey는 `Relationship`으로 정의되어 있다.

|     DB     |    CoreData    |
|:----------:|:--------------:|
|    Table   |     Entity     |
|:----------:|:--------------:|
|    Field   |    Attribute   |
|:----------:|:--------------:|
| ForeignKey |  Relationship  |

* Schema

  ![coredata_schema](/assets/images/coreData/coredata_schema.png)

* CoreDataTest.xcdatamodeld 선택 후 Add Entity > Entity 이름 변경 > Attributes 추가

  ![coredata_create_schema](/assets/images/coreData/coredata_create_schema.png)

* Xcode Navigation bar > Edit > Create NSManagedObject Subclass... 선택

  ![coredata_create_manage_object_subclass](/assets/images/coreData/coredata_create_manage_object_subclass.png)

  `Chat+CoreDataClass.swift`와 `Chat+CoreDataProperties.swift`가 자동 생성된다.

  ![coredata_create_manage_object_subclass2](/assets/images/coreData/coredata_create_manage_object_subclass2.png)

---
<h4> AppDelegate.swift 수정 </h4>
  * Database에 Data를 Insert, Update, Delete 하기 위해 AppDldegate.swift에 아래와 같은 코드를 추가

  {% highlight swift %}
  // AppDelegate.swift

  // MARK: - Core Data Stack set

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

    print(url)

    var error: NSError? = nil
    var failureReason = "There was an error creating or loading the application's saved data."
    let mOptions = [NSMigratePersistentStoresAutomaticallyOption: true,
                    NSInferMappingModelAutomaticallyOption: true]

    do
    {
        try coordinator!.addPersistentStore(ofType: NSSQLiteStoreType, configurationName: nil, at: url, options: mOptions)
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
<h4> 참고 </h4>

[Apple Developer Core Data](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/CoreData/index.html)

[core-data-and-swift by Bart Jacobs](https://code.tutsplus.com/series/core-data-and-swift--cms-907)
