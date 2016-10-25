---
layout: post
title:  "iOS Core Data in Swift #3 Migration"
date:   2016-10-25 10:42:00 +09
categories: Study
---

#2에 이어서 Core Data를 수정해보자.

처음부터 완벽하게 DB설계를 하면 문제가 없겠지만, 현실은 수정... 수정.. 수.. ㅅ.... 읍

Chat Entity에 Attribute 추가 후 App 실행 도중 아래와 같은 에러가 발생하였다.

    ...

    options:(null) ... returned error Error Domain=NSCocoaErrorDomain Code=134100

    ...

    reason = "The model used to open the store is incompatible with the one used to create the store"

sqlite 파일이 없으면 만들고, 있으면 loading하는데,
loading 중 .sqlite 파일과 Core Data 파일(model)에 명시된 정보가 일치하지 않아 발생

그럼 어떻게해요? CoreData를 **Migration** 하면 되지!

---

* [#1 Core Data 설정하기](https://ham1955burger.github.io/study/2016/10/20/iOS-coredata-in-chapter-1.html)

* [#2 Core Data를 이용해 Record 다뤄보기](https://ham1955burger.github.io/study/2016/10/20/iOS-coredata-in-chapter-2.html)

* <h4> #3 Core Data Migration 하기 </h4>

---

<h4> Model 버전 추가하기 </h4>

Core Data를 수정 하기 전에, Model 버전을 추가해야한다.

* Core Data file 선택 > Xcode navigation bar > Editor > Add Model Version... 선택

  ![coredata_add_model_version](/assets/images/coreData_chapter_3/coredata_add_model_version.png)

  CoreDataTest 2가 추가 된 것 확인

  ![coredata_add_model_version_success](/assets/images/coreData_chapter_3/coredata_add_model_version_success.png)

* Core Data 수정

  Chat Entity에 image Attribute 추가

  ![coredata_update_chat_entity](/assets/images/coreData_chapter_3/coredata_update_chat_entity.png)

* Model 버전을 CoreDataTest 2로 변경하기

  우측 설정 창 > Model Version > current > CoreDataTest 2 선택

  ![coredata_model_version_setting](/assets/images/coreData_chapter_3/coredata_model_version_setting.png)

  아래와 같이 초록 동그라미 체크가 CoreDataTest 2로 옮겨진 것 확인

  ![coredata_model_version_setting_success](/assets/images/coreData_chapter_3/coredata_model_version_setting_success.png)



* Managed Object Subclass 수정

  type을 안다면 코드로 추가해도 되고, #1과 같이 Xcode navigation bar > Editor > Create NSManagedObject Subclass... 를 선택해도 된다. 난 후자 선택

---

<h4> Migration Option 추가하기 </h4>

.sqlite파일이 loading될때, Option을 주어 Core Data 파일에 맞게 자동으로 Migration 되게 해보자


{% highlight swift %}
  // AppDelegate.swift

  ...

  lazy var persistentStoreCoordinator: NSPersistentStoreCoordinator? = {
        // The persistent store coordinator for the application. This implementation creates and return a coordinator, having added the store for the application to it. This property is optional since there are legitimate error conditions that could cause the creation of the store to fail.
        // Create the coordinator and store
        var coordinator: NSPersistentStoreCoordinator? = NSPersistentStoreCoordinator(managedObjectModel: self.managedObjectModel)
        let url = self.applicationDocumentsDirectory.appendingPathComponent("CoreDataTest.sqlite")

        var error: NSError? = nil
        var failureReason = "There was an error creating or loading the application's saved data."

        // auto migration options
        let mOptions = [NSMigratePersistentStoresAutomaticallyOption: true,
                        NSInferMappingModelAutomaticallyOption: true]

        do
        {
            // option에 auto migration options 추가
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
{% endhighlight %}

이상 무!

![coredata_add_attribute_success](/assets/images/coreData_chapter_3/coredata_add_attribute_success.png)

---

<h4> 마무리 </h4>

NSFetchRequest 옵션(조건 조회)에 대하여 더 조사할 것. 추후 #4에서..
