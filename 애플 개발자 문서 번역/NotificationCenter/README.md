# NotificationCenter
> 등록 된 옵저버에게 정보를 브로드 캐스팅 할 수 있게하는 노티피케이션 디스패치 메커니즘이다.


* [Overview](#overview)
* [Topics](#topics)
    * [Getting the Default Notification Center](#getting-the-default-notification-center)
    * [Adding and Removing Notification Observers](#adding-and-removing-notification-observers)
    * [Posting Notifications](#posting-notifications)
* [Relationships](#relationships)
* [See Also](#see-also)


&nbsp;
## Overview
객체는 [addObserver(_:selector:name:object:)](https://developer.apple.com/documentation/foundation/notificationcenter/1415360-addobserver) 또는 [addObserver(forName:object:queue:using:)](https://developer.apple.com/documentation/foundation/notificationcenter/1411723-addobserver) 메서드를 사용하여 노티피케이션([NSNotification](https://developer.apple.com/documentation/foundation/nsnotification) 객체)을 수신하기 위해 노티피케이션 센터에 등록한다. 객체가 옵저버로 추가되면 객체는 수신해야하는 노티피케이션을 지정한다. 객체는, 이 메서드를 몇 차례 호출 해, 몇 개의 다른 노티피케이션의 옵저버로서 자신을 등록할 수 있다.


실행중인 각 앱에는 [default](https://developer.apple.com/documentation/foundation/notificationcenter/1414169-default) 노티피케이션 센터가 있으며 새로운 노티피케이션 센터를 만들어 특정 상황에서 통신을 구성할 수 있다.


노티피케이션 센터는 단일 프로그램 내에서만 노티피케이션을 전송할 수 있다. 다른 프로세스에 노티피케이션을 게시하거나 다른 프로세스에서 노티피케이션을 받으려면 대신 [DistributedNotificationCenter](https://developer.apple.com/documentation/foundation/distributednotificationcenter)를 사용한다.


&nbsp;
## Topics
### Getting the Default Notification Center
> 기본 노티피케이션 센터 얻기

* `class var `default`: NotificationCenter`
    * 앱의 기본 노티피케이션 센터이다.
    

### Adding and Removing Notification Observers
> 노티피케이션 옵저버 추가 및 제거

* `func addObserver(forName: NSNotification.Name?, object: Any?, queue: OperationQueue?, using: (Notification) -> Void) -> NSObjectProtocol` 
    * 노티피케이션 큐 및 큐에 추가할 블록과 옵셔널 노티피케이션 이름 및 센더를 포함하는 노티피케이션 센터의 디스패치 테이블에 항목을 추가한다. 
* `func addObserver(Any, selector: Selector, name: NSNotification.Name?, object: Any?)` 
    * 옵저버 및 노티피케이션 셀렉터와 옵셔널 노티피케이션 이름 및 센더를 사용하여 노티피케이션 센터의 디스패치 테이블에 항목을 추가한다.
* `func removeObserver(Any, name: NSNotification.Name?, object: Any?)` 
    * 노티피케이션 센터의 디스패치 테이블에서 일치하는 항목을 제거한다.
* `func removeObserver(Any)` 
    * 노티피케이션 센터의 디스패치 테이블에서 지정된 옵저버를 지정하는 모든 항목을 제거한다.
    

### Posting Notifications
> 노티피케이션 게시

* `func post(Notification)` 
    * 지정된 노티피케이션을 노티피케이션 센터에 게시한다.
* `func post(name: NSNotification.Name, object: Any?, userInfo: [AnyHashable : Any]? = nil)` 
    * 지정된 이름, 센더 및 정보로 노티피케이션을 만들고 노티피케이션 센터에 게시한다.
* `func post(name: NSNotification.Name, object: Any?)` 
    * 지정된 이름, 센더로 노티피케이션을 만들고 노티피케이션 센터에 게시한다.


&nbsp;    
## Relationships
### Inherits From
* NSObject


### Conforms To
* CVarArg, Equatable, Hashable


&nbsp;    
## See Also
### Notifications
* struct Notification
* class NotificationQueue


&nbsp;      
&nbsp;      
### [by. 0junChoi](https://github.com/0jun0815) email: <0jun0815@gmail.com>
### [Apple Developer Documentation NotificationCenter](https://developer.apple.com/documentation/foundation/notificationcenter)
