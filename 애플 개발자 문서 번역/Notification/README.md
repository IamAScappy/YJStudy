# Notification
> 모든 등록 된 옵저버에게 노티피케이션 센터를 통해 정보를 전달하기위한 컨테이너.


* [Topics](#topics)
    * [Creating a Notification](#creating-a-notification)
    * [Getting Notification Information](#getting-notification-information)
    * [Comparing Notifications](#comparing-notifications)
    * [Describing a Notification](#describing-a-notification)
* [Relationships](#relationships)
* [See Also](#see-also)


&nbsp;
## Topics
### Creating a Notification
> 노티피케이션 생성

* `init(name: Notification.Name, object: Any?, userInfo: [AnyHashable : Any]?)`
    * 새로운 노티피케이션을 초기화한다.
* `typealias Notification.Name`
    * 노티피케이션의 이름을 나타내기 위해서 사용되는 타입의 별명(알리아스)이다.
* `struct NSNotification.Name`
    * 노티피케이션의 이름으로 사용되는 타입이다.
    

### Getting Notification Information
> 노티피케이션 정보 얻기

* `var name: Notification.Name`
    * 노티피케이션을 식별하는 태그.
* `var object: Any?`
    * 포스터가 옵저버에게 보내려고 하는 객체이다.
* `var userInfo: [AnyHashable : Any]?`
    * 이 노티피케이션과 관련된 값 또는 객체의 저장소이다.
    

### Comparing Notifications
> 노티피케이션 비교

* `static func != (Notification, Notification) -> Bool`
    * 두 값이 같지 않은지 여부를 나타내는 부울 값을 반환한다.
* `static func == (Notification, Notification) -> Bool`
    * 두 개의 노티피케이션이 동일한 지 여부를 비교한다.
    

### Describing a Notification
> 노티피케이션 설명

* `var description: String`
    * 노티피케이션에 대한 텍스트 설명.
* `var debugDescription: String`
    * 디버깅에 적합한 노티피케이션의 텍스트 설명.
* `var customMirror: Mirror`
    * 노티피케이션을 반영하는 미러.
* `var hashValue: Int`
    * 노티피케이션에 대해 계산 된 해시 값이다.
    

### Using Reference Types
> 참조 타입 사용

* `class NSNotification`
    * [Noticiation](https://developer.apple.com/documentation/foundation/notification)에 브리징하는 등록 된 옵저버에게 방송 된 정보를 포함하는 객체. 참조 의미론 또는 기타 Foundation 관련 동작이 필요할 때 NSNotification을 사용한다.
* `typealias Notification.ReferenceType`
    * 이 값 타입의 해당 참조 타입에 대한 별명이다.


&nbsp;    
## Relationships
### Conforms To
* CustomReflectable
* Equatable
* Hashable
* ReferenceConvertible


&nbsp;    
## See Also
### Notifications
* class NotificationCenter
* class NotificationQueue


&nbsp;      
&nbsp;      
### [by. 0junChoi](https://github.com/0jun0815) email: <0jun0815@gmail.com>
### [Apple Developer Documentation Notification](https://developer.apple.com/documentation/foundation/notification)
