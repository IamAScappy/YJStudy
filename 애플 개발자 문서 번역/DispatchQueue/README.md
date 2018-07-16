# DispatchQueue
> DispatchQueue는 작업 항목의 실행을 관리한다. 큐에 제출 된 각 작업 항목은 시스템이 관리하는 스레드 풀에서 처리된다.


* [Topics](#topics)
    * [Initializers](#initializers)
    * [Instance Methods](#instance-methods)
    * [Instance Properties](#instance-properties)
    * [Type Properties](#type-properties)
    * [Type Methods](#type-methods)
    * [Structures](#structures)
    * [Enumerations](#enumerations)
* [Relationships](#relationships)
* [See Also](#see-also)


&nbsp;
## Topics
### Initializers
* `init(__label: UnsafePointer<Int8>?, attr: __OS_dispatch_queue_attr?)`
    * 블록을 제출할 수 있는 새로운 디스패치 큐를 만든다.
* `init(__label: UnsafePointer<Int8>?, attr: __OS_dispatch_queue_attr?, queue: DispatchQueue?)`
* `init(label: String, qos: DispatchQoS, attributes: DispatchQueue.Attributes, autoreleaseFrequency: DispatchQueue.AutoreleaseFrequency, target: DispatchQueue?)`


### Instance Methods
* `func sync(execute: () -> Void)`
    * 디스패치 큐에서 실행을 위해 블록 객체를 제출하고 해당 블록이 완료 될 때까지 대기한다.
* `func async(execute: DispatchWorkItem)`
* `func async(group: DispatchGroup, execute: DispatchWorkItem)`
* `func async(group: DispatchGroup?, qos: DispatchQoS, flags: DispatchWorkItemFlags, execute: () -> Void)`
* `func asyncAfter(deadline: DispatchTime, execute: DispatchWorkItem)`
* `func asyncAfter(deadline: DispatchTime, qos: DispatchQoS, flags: DispatchWorkItemFlags, execute: () -> Void)`
* `func asyncAfter(wallDeadline: DispatchWallTime, execute: DispatchWorkItem)`
* `func asyncAfter(wallDeadline: DispatchWallTime, qos: DispatchQoS, flags: DispatchWorkItemFlags, execute: () -> Void)`
* `func getSpecific<T>(key: DispatchSpecificKey<T>) -> T?`
* `func setSpecific<T>(key: DispatchSpecificKey<T>, value: T?)`
* `func sync<T>(execute: () -> T) -> T`
* `func sync(execute: DispatchWorkItem)`
* `func sync<T>(flags: DispatchWorkItemFlags, execute: () -> T) -> T`


### Instance Properties
* `var label: String`
* `var qos: DispatchQoS`


### Type Properties
* `class var main: DispatchQueue`


### Type Methods
* `class func concurrentPerform(iterations: Int, execute: (Int) -> Void)`
* `class func getSpecific<T>(key: DispatchSpecificKey<T>) -> T?`
* ~~class func global(priority: DispatchQueue.GlobalQueuePriority) -> DispatchQueue~~
    * **더이상 사용되지 않음**
* `class func global(qos: DispatchQoS.QoSClass) -> DispatchQueue`


### Structures
* `struct DispatchQueue.Attributes`


### Enumerations
* `enum DispatchQueue.AutoreleaseFrequency`
* `enum DispatchQueue.GlobalQueuePriority`


&nbsp;    
## Relationships
### Inherits From
* DispatchObject


### Conforms To
* CVarArg, Equatable, Hashable


&nbsp;    
## See Also
### Managing Dispatch Queues
* struct DispatchQueue.Attributes
* class DispatchSpecificKey
* enum DispatchQueue.AutoreleaseFrequency
* ~~func dispatch_get_current_queue() -> DispatchQueue~~
* func setTarget(queue: DispatchQueue?)
* func dispatchMain() -> Never


&nbsp;      
&nbsp;      
### [by. 0junChoi](https://github.com/0jun0815) email: <0jun0815@gmail.com>
### [Apple Developer Documentation DispatchQueue](https://developer.apple.com/documentation/dispatch/dispatchqueue)
