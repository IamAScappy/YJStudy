# NotificationQueue
> 노티피케이션 센터 버퍼


* [Overview](#overview)
* [Topics](#topics)
    * [Creating Notification Queues](#creating-notification-queues)
    * [Getting the Default Queue](#getting-the-default-queue)
    * [Managing Notifications](#managing-notifications)
    * [Constants](#constants)
* [Relationships](#relationships)
* [See Also](#see-also)


&nbsp;
## Overview
노티피케이션 센터가 게시 될 때 노티피케이션 센터를 배포하는 반면, 큐에 배치 된 노티피케이션은 현재 실행 루프가 끝날 때가지 또는 실행 루프가 유휴 상태가 될 때 까지 지연 될 수 있다. 중복 된 노티피케이션을 병합하여 여러 노티피케이션이 게시 되더라도 하나의 노티피케이션만 전송되도록 할 수 있다.


노티피케이션 큐는 선입 선출(FIFO) 순서로 노티피케이션을 유지한다. 노티피케이션이 큐의 맨 앞으로 이동하면 큐는 노티피케이션 센터에 게시하고 옵저버로 등록 된 모든 객체에 노티피케이션을 전달한다.


모든 스레드에는 프로세스의 기본 노티피케이션 센터와 연결된 기본 노티피케이션 큐가 있다. 고유한 노티피케이션 큐를 만들고 센터 및 스레드 별로 여러 개의 큐를 만들 수 있다.


&nbsp;
## Topics
### Creating Notification Queues
> 노티피케이션 큐 생성

* `init(notificationCenter: NotificationCenter)`
    * 지정된 노티피케이션 센터에 대한 노티피케이션 큐를 초기화하고 반환한다.
    

### Getting the Default Queue
> 기본 큐 얻기

* `class var `default`: NotificationQueue`
    * 현재의 스레드의 기본 노티피케이션 큐를 반환한다.
    

### Managing Notifications
> 노티피케이션 관리

* `func enqueue(Notification, postingStyle: NotificationQueue.PostingStyle, coalesceMask: NotificationQueue.NotificationCoalescing, forModes: [RunLoop.Mode]?)`
    * 지정된 게시 스타일, 병합 기준 및 루프 모드를 실행하여 노티피케이션 큐에 노티피케이션을 추가한다.
* `func enqueue(Notification, postingStyle: NotificationQueue.PostingStyle)`
    * 지정된 게시 스타일을 사용하여 노티피케이션 큐에 노티피케이션을 추가한다.
* `func dequeueNotifications(matching: Notification, coalesceMask: Int)`
    * 제공된 일치 기준을 사용하여 제공된 노티피케이션과  일치하는 모든 노티피케이션을 큐에서 제거한다.


### Constants

* `struct NotificationQueue.NotificationCoalescing`
    * 노티피케이션이 병합되는 방법을 지정하는 상수.
* `enum NotificationQueue.PostingStyle`
    * 노티피케이션을 게시 할 시기를 지정하는 상수.
    

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
* class NotificationCenter


&nbsp;      
&nbsp;      
### [by. 0junChoi](https://github.com/0jun0815) email: <0jun0815@gmail.com>
### [Apple Developer Documentation NotificationQueue](https://developer.apple.com/documentation/foundation/notificationqueue)
