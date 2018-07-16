# DispatchQoS
> DispatchQoS는 서비스 품질 클래스를 캡슐화한다.


* [Overview](#overview)
* [Topics](#topics)
    * [Initializers](#initializers)
    * [Instance Properties](#instance-properties)
    * [Type Properties](#type-properties)
    * [Operator Functions](#operator-functions)
    * [Enumerations](#enumerations)
* [Relationships](#relationships)
* [See Also](#see-also)


&nbsp;
## Overview
QoS(Quality of Service) 클래스는 [DispatchQueue](https://developer.apple.com/documentation/dispatch/dispatchqueue)에서 수행 할 작업을 분류한다. 작동시킬 QoS를 지정함으로써 중요도를 표시하고 시스템이 우선 순위를 정하고 이에 따라 일정을 정한다.


우선 순위가 높은 작업은 우선 순위가 낮은 작업보다 더 빨리 수행되고 자원이 많으므로 일반적인 우선 순위가 낮은 작업보다 많은 에너지가 필요하다. 앱이 수행하는 작업에 적합한 QoS 클래스를 정확하게 지정하면 앱이 반응적이고 에너지 효율적이라는 것을 보장 할 수 있다.


&nbsp;
## Topics
### Initializers
* `init(qosClass: DispatchQoS.QoSClass, relativePriority: Int)`
    * 지정된 QoS 클래스 및 상대적 우선 순위로 새 DispatchQoS 객체를 만든다.
    

### Instance Properties
* `let qosClass: DispatchQoS.QoSClass`
    * 서비스 품질(QoS) 클래스.
* `let relativePriority: Int`
    * 같은 등급의 다른 것들과 비교 한 서비스 품질의 우선 순위.
    

### Type Properties
* `static let background: DispatchQoS`
    * 서비스 클래스의 백그라운드 품질.
* `static let 'default': DispatchQoS`
    * 서비스 클래스의 기본 품질.
* `static let unspecified: DispatchQoS`
    * 서비스 클래스의 부재 품질.
* `static let userInitiated: DispatchQoS`
    * 사용자가 시작한 서비스 품질 클래스이다.
* `static let userInteractive: DispatchQoS`
    * 사용자 대화형 서비스 품질 클래스이다.
* `static let utility: DispatchQoS`
    * 서비스 품질 클래스의 유틸리티이다.
    

### Operator Functions
* `static func != (DispatchQoS, DispatchQoS) -> Bool`
    * 두 값이 같지 않은지 여부를 나타내는 부울 값을 반환한다.
* `static func == (DispatchQoS, DispatchQoS) -> Bool`


### Enumerations
* `enum DispatchQoS.QoSClass`
    * DispatchQoS.QoSClass는 서비스 품질 클래스를 캡슐화한다.


&nbsp;    
## Relationships
### Inherits From
* DispatchObject


### Conforms To
* CVarArg, Equatable, Hashable


&nbsp;    
## See Also
### Prioritizing Work and Specifying Quality of Service
* enum DispatchQoS.QoSClass
* [Dispatch Queue Priorities](https://developer.apple.com/documentation/dispatch/dispatch_queue_priorities)


&nbsp;      
&nbsp;      
### [by. 0junChoi](https://github.com/0jun0815) email: <0jun0815@gmail.com>
### [Apple Developer Documentation DispatchQoS](https://developer.apple.com/documentation/dispatch/dispatchqos)
