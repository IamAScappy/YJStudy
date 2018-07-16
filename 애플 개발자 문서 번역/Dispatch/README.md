# 적
> 시스템이 관리하는 디스패치 큐에 작업을 제출하여 멀티 코어 하드웨어에서 동시에 코드를 실행한다.


* [Overview](#overview)
* [Topics](#topics)
    * [Managing Dispatch Queues](#managing-dispatch-queues)
    * [Synchronous and Asynchronous Execution](#synchronous-and-asynchronous-execution)
    * [Serial and Concurrent Queues](#serial-and-concurrent-queues)
    * [System-Provided Queues](#system-provided-queues)
    * [Managing Units of Work](#managing-units-of-work)
    * [Prioritizing Work and Specifying Quality of Service](#prioritizing-work-and-specifying-quality-of-service)
    * [Using Dispatch Groups](#using-dispatch-groups)
    * [Using Dispatch Semaphores](#using-dispatch-semaphores)
    * [Using Dispatch Data](#using-dispatch-data)
    * [Using Dispatch Time](#using-dispatch-time)
    * [Managing Dispatch Sources](#managing-dispatch-sources)
    * [Managing Dispatch I/O](#managing-dispatch-i/o)
    * [Working with Dispatch Objects](#working-with-dispatch-objects)
    * [Enumerations](#enumerations)
    * [Classes](#classes)
    * [Protocols](#protocols)
    * [Reference](#reference)


&nbsp;    
## Overview
디스패치는 macOS, iOS, watchOS 및 tvOS의 멀티 코어 하드웨어에서 동시 코드 실행에 대한 체계적이고 포괄적인 개선을 제공하는 언어 기능, 런타임 라이브러리 및 시스템 향상으로 구성된다.


BSD 서브 시스템, Core Foundation 및 Cocoa API 모두가 이러한 향상된 기능을 사용하도록 확장되어 시스템과 애플리케이션이 보다 빠르고 효율적으로 실행되고 응답성이 개선되었다. 단일 애플리케이션이 여러 코어를 효과적으로 사용하는 것이 얼마나 어려운지 고려한다. 서로 다른 수의 컴퓨팅 코어를 사용하는 다른 컴퓨터 또는 이러한 코어를 위해 경쟁하는 여러 애플리케이션이 있는 환경에서 이 작업을 수행할 수 있다. 시스템 수준에서 작동하는 GCD는 실행중인 모든 애플리케이션의 요구 사항을 균형있게 사용할 수 있는 시스템 자원과 보다 잘 일치 시킬 수 있다.


&nbsp;
## Topics
###  Managing Dispatch Queues
> 디스패치 큐 관리

GCD는 애플리케이션이 블록 객체 형태로 작업을 제출할 수 있는 FIFO 큐를 제공하고 관리한다. 디스패치 큐에 제출 된 작업은 시스템에서 완전히 관리되는 스레드 풀에서 실행된다. 작업이 실행되는 스레드에 대해서는 아무런 보증도하지 않는다.


#### Synchronous and Asynchronous Execution
> 동기 및 비동기 실행

각 작업 항목(item)은 동기적 또는 비동기적으로 실행될 수 있다. 작업 항목이 sync 메서드와 동기적으로 실행되면 프로그램은 메서드 호출이 반환되기 전에 실행이 완료 될 때까지 대기한다. 작업 항목이 async 메서드와 비동기 적으로 실행되면 메서드 호출이 즉시 반환된다.


#### Serial and Concurrent Queues
> 직렬 및 동시 큐

디스패치 큐는 작업 항목이 한 번에 하나씩 실행되도록 직렬 일 수도 있고, 또는 동시적일 수 있으므로 작업 항목이 순서에 따라 큐에서 제거되지만 한 번에 모두 실행되고 모든 순서로 완료 될 수 있다. 직렬 및 동시 큐 모두 작업 항목을 선입 선출(FIFO) 순서로 처리한다.


#### System-Provided Queues
> 시스템 제공 대기열

애플리케이션이 시작되면 시스템은 자동으로 메인 큐라는 특수 큐를 만든다. 메인 큐에 포함된 작업 항목은 앱의 메인 스레드에서 직렬로 실행된다. main 타입 프로퍼티를 사용하여 메인 큐에 접근 할 수 있다.


> **Important**
>
> 메인 큐에서 작업 항목을 동기적으로 실행하려고하면 데드록이 발생한다.


직렬 메인 큐 외에도 시스템은 다수의 전역 동시 디스패치 큐를 생성한다. global(attributes:) 타입 메서드를 사용하여 지정된 서비스 품질(QoS)과 가장 일치하는 전역 동시 큐에 접근 할 수 있다.


* `class DispatchQueue`
    * DispatchQueue는 작업 항목의 실행을 관리한다. 큐에 제출 된 각 작업 항목은 시스템이 관리하는 스레드 풀에서 처리된다.
* `struct DispatchQueue.Attributes`
* `class DispatchSpecificKey`
    * DispatchSpecificKey는 setSpecific<T>(key:value:) 메서드를 사용하여 DispatchQueue의 컨텍스트 값과 연관 될 수 있는 키를 캡슐화하고 getSpecific<T>(key:) 메서드를 사용하여 접근하는 키이다.
* `enum DispatchQueue.AutoreleaseFrequency`
* ~~func dispatch_get_current_queue() -> DispatchQueue~~
    * 현재 실행중인 블록이 실행중인 큐를 반환한다. **더이상 사용되지 않음**
* `func setTarget(queue: DispatchQueue?)`
    * 지정된 객체의 타겟 큐를 설정한다.
* `func dispatchMain() -> Never`
    * 메인 큐에 제출 된 블록을 실행한다.


### Managing Units of Work
> 작업 단위 관리

작업 항목을 사용하면 개별 작업 단위의 프로퍼티를 직접 구성할 수 있다. 또한 개별 작업 단위를 완료 할 때까지 기다리고, 완료에 대한 알림을 받고, 취소 할 목적으로 개별 작업 단위를 처리 할 수 있다.


* `class DispatchWorkItem`
    * DispatchWorkItem은 수행 할 수 있는 작업을 캡슐화한다. 작업 항목은 DispatchQueue 및 DispatchGroup 내에 디스패치 할 수 있다. DispatchWorkItem은 DispatchSource 이벤트, 등록 또는 취소 핸들러로 설정할 수도 있다.
* `struct DispatchWorkItemFlags`
    * DispatchWorkItemFlags는 [DispatchWorkItem](https://developer.apple.com/documentation/dispatch/dispatchworkitem) 값의 동작을 구성하는 옵션 집합으로, 서비스 클래스 품질 및 장벽(배리어)를 만들거나 분리 된 새 스레드를 생성할지 여부를 포함한다.

### Prioritizing Work and Specifying Quality of Service
> 작업 우선 순위 지정 및 서비스 품질 지정

* `struct DispatchQoS`
    * DispatchQoS는 서비스 품질 클래스를 캡슐화한다.
* `enum DispatchQoS.QoSClass`
    * DispatchQoS.QoSClass는 서비스 품질 클래스를 캡슐화한다.
* [Dispatch Queue Priorities](https://developer.apple.com/documentation/dispatch/dispatch_queue_priorities)
    * 적절한 전역 동시 큐를 선택하는 데 사용된다.
    

### Using Dispatch Groups
> 디스패치 그룹 사용

블록을 그룹화하면 집계 동기화가 가능하다. 애플리케이션은 여러 큐에서 실행될 수 있더라도 여러 블록을 제출하고 모두 완료되면 추적 할 수 있다. 이 작업은 지정된 모든 작업이 완료 될 때까지 진행할 수 없을 때 도움이 될 수 있다.


* `class DispatchGroup`
    * DispatchGroup은 작업의 집계 동기화를 허용한다. 여러 큐에서 실행될 수 있는 경우에도 이를 사용하여 여러 다른 작업 항목을 제출하고 모두 완료 할 때 추적할 수 있다. 이 작업은 지정된 모든 작업이 완료 될 때까지 진행할 수 없을 때 도움이 될 수 있다.
    

### Using Dispatch Semaphores
> 디스패치 세마포어 사용

디스패치 세마포어는 전통적인 카운팅 세마포어를 효율적으로 구현 한 것이다. 디스패치 세마포어는 호출 스레드를 차단해야하는 경우에만 커널을 호출한다. 호출하는 세마포어를 차단할 필요가 없다면, 커널 호출은 일어나지 않는다.


* `class DispatchSemaphore`
    * DispatchSemaphore는 여러 실행 컨텍스트에서 자원에 대한 접근을 제어하는 데 사용할 수 있는 전통적인 카운팅 세마포어를 효율적으로 구현한다. 


### Using Dispatch Data
> 디스패치 데이터 사용

* `struct DispatchData`
    * DispatchData 객체는 메모리 기반 데이터 버퍼를 관리한다. 데이터 버퍼는 인접한 메모리 블록으로 노출되지만 내부적으로는 여러 개의 불연속 메모리 블록으로 구성 될 수 있다.
* `struct DispatchDataIterator`
* `enum DispatchData.Deallocator`

### Using Dispatch Time
> 디스패치 타임 사용

* `struct DispatchTime`
    * DispatchTime은 나노 초 정밀도의 기본 클럭을 기준으로한 특정 시점을 나타낸다. Apple 플랫폼에서 기본 시계는 마하 절대 시간 단위를 기반으로한다.
* [Dispatch Time Constants](https://developer.apple.com/documentation/dispatch/dispatch_time_constants)
    * 기본 시간 상수.
* `struct DispatchWallTime`
    * DispatchTime은 마이크로 초 정밀도의 벽 시계에 따라 절대 시간을 나타낸다. Apple 플랫폼에서 기본 시계는 gettimeofday(2)의 결과를 기반으로 한다.
* `enum DispatchTimeInterval`
    * DispatchTimeInterval은 초, 밀리 초, 마이크로 초 또는 나노 초 수를 나타낸다. DispatchTimeInterval 값을 사용하여 [DispatchSourceTimer](https://developer.apple.com/documentation/dispatch/dispatchsourcetimer)가 발생하거나 [DispatchIO](https://developer.apple.com/documentation/dispatch/dispatchio) 채널에 대한 I/O 핸들러가 호출되는 간격을 지정하고 [DispatchTime](https://developer.apple.com/documentation/dispatch/dispatchtime) 값을 증가 및 감소 시킨다.
* `enum DispatchTimeoutResult`
    * DispatchTimeoutResult는 지정된 시간 전에 디스패치 작업이 완료되었는지 여부를 나타낸다.
* [Dispatch Time Operators](https://developer.apple.com/documentation/dispatch/dispatch_time_operators)
* `typealias dispatch_time_t`
    * 다소 추상적인 시간 표현.
    

### Managing Dispatch Sources
> 디스패치 소스 관리

* `class DispatchSource`
    * DispatchSource는 마하 포트, 유닉스 설명자(디스크립터), 유닉스 신호, VFS 노드와 같은 저레벨 시스템 객체를 모니터링하여 작업을 위한 인터페이스를 제공하고 이러한 활동이 발생했을 때 비동기 처리를 위해 큐를 디스패치하는 이벤트 핸들러를 제출한다.
* `protocol DispatchSourceProtocol`
    * 모든 디스패치 소스 타입과 공유되는 공통 프로퍼티 및 메서드 집합을 정의한다.
* `protocol DispatchSourceFileSystemObject`
    * DispatchSource.FileSystemEvent에 의해 정의 된 이벤트에 대한 파일 설명자를 모니터하는 디스패치 소스이다. 핸들은 파일 설명자(Int32)이다.
* `struct DispatchSource.FileSystemEvent`
    * 파일 시스템 이벤트.
* `protocol DispatchSourceMachSend`
    * 마하 포트를 모니터하여 데드 네임 통지(노티피케이션)를하는 디스패치 소스. 송신 권리가 대응하는 수신 권한을 더 이상 갖지 않음을 나타낸다. 핸들은 송신 또는 send-once 권한이 있는 마하 포트이다(mach_port_t).
* `struct DispatchSource.MachSendEvent`
    * 마하 포트 송신 이벤트.
* `protocol DispatchSourceMachReceive`
    * 보류중인 메시지에 대한 마하 포트를 모니터링하는 디스패치 소스. 핸들은 수신 권한이 있는 마하 포트이다(mach_port_t).
* `protocol DispatchSourceMemoryPressure`
    * 메모리 압력 조건의 변화를 시스템에서 모니터링하는 디스패치 소스.
* `struct DispatchSource.MemoryPressureEvent`
    * 메모리 압력 이벤트.
* `protocol DispatchSourceProcess`
    * [DispatchSource.ProcessEvent](https://developer.apple.com/documentation/dispatch/dispatchsource/processevent)에 의해 정의 된 이벤트에 대해 외부 프로세스를 모니터링하는 디스패치 소스이다. 핸들은 프로세스 식별자([pid_t](https://developer.apple.com/documentation/kernel/pid_t))이다.
* `struct DispatchSource.ProcessEvent`
    * 프로세스 이벤트.
* `protocol DispatchSourceTimer`
    * 타이머를 기반으로 이벤트 핸들러 블록을 전송하는 디스패치 소스이다.
* `struct DispatchSource.TimerFlags`
* `protocol DispatchSourceUserDataAdd`
    * [add(data:)](https://developer.apple.com/documentation/dispatch/dispatchsourceuserdataadd/2300018-add) 메서드에 대한 호출을 통해 얻은 데이터를 병합하는 디스패치 소스이다.
* `protocol DispatchSourceUserDataOr`
    * [or(data:)](https://developer.apple.com/documentation/dispatch/dispatchsourceuserdataor/2300090-or) 메서드를 호출하여 얻은 데이터를 병합하는 디스패치 소스이다.
* [Dispatch Source Mach Send Event Flags](https://developer.apple.com/documentation/dispatch/dispatch_source_mach_send_event_flags)
    * 마하 송신 이벤트 플래그.
* [Dispatch Source Process Event Flags](https://developer.apple.com/documentation/dispatch/dispatch_source_process_event_flags)
    * 프로세스 이벤트 플래그.
* [Dispatch Source Vnode Event Flags](https://developer.apple.com/documentation/dispatch/dispatch_source_vnode_event_flags)
    * 파일 시스템 객체 이벤트 플래그.
* [Dispatch Source Memory Pressure Event Flags](https://developer.apple.com/documentation/dispatch/dispatch_source_memory_pressure_event_flags)
    * 메모리 압력 이벤트 플래그.
* `protocol DispatchSourceSignal`
    * 유닉스 신호에 대한 현재 프로세스를 모니터하는 디스패치 소스.
* `protocol DispatchSourceRead`
* `protocol DispatchSourceWrite`


### Managing Dispatch I/O
> 디스패치 I/O 관리

디스패치 I/O 채널 API를 사용하여 파일 설명자 기반 작업을 관리 할 수 있다. 이 API는 파일 설명자의 내용에 접근하기 위해 스트림 기반 및 임의 접근 방식을 모드 지원한다.


* `class DispatchIO`
    * DispatchIO는 파일 설명자의 콘텐츠에 접근하기 위해 스트림 기반 및 임의 접근 방식을 사용하여 파일 설명자에 대한 작업을 수행하는 채널을 제공한다.
* `enum DispatchIO.StreamType`
    * 파일 설명자의 콘텐츠에 접근하기 위한 의미론.
* `struct DispatchIO.CloseFlags`
    * I/O 채널을 닫는 의미론.
* `struct DispatchIO.IntervalFlags`
* [Dispatch I/O Channel Types](https://developer.apple.com/documentation/dispatch/dispatch_i_o_channel_types)
    * 작성 될 수 있는 디스패치 I/O 채널의 타입.
* [Dispatch I/O Channel Closing Options](https://developer.apple.com/documentation/dispatch/dispatch_i_o_channel_closing_options)
    * 디스패치 I/O 채널을 닫을 때 사용할 옵션.
* [Dispatch I/O Channel Configuration Options](https://developer.apple.com/documentation/dispatch/dispatch_i_o_channel_configuration_options)
    * 채널을 구성 할 때 사용할 옵션.
    

### Working with Dispatch Objects
> 디스패치 객체 작업


GCD는 애플리케이션이 메모리 관리, 실행 일시 중지 및 다시 시작, 객체 컨텍스트 정의 및 작업 데이터 로깅과 같은 처리 측면을 관리 할 수 있도록 디스패치 객체 인터페이스를 제공한다. 디스패치 객체는 수동으로 보유(retained) 및 출시(released)되어야하며 가비지 수집되지 않아야한다.


* `class DispatchObject`
    * DispatchObject는 [DispatchQueue](https://developer.apple.com/documentation/dispatch/dispatchqueue), [DispatchGroup](https://developer.apple.com/documentation/dispatch/dispatchgroup) 및 [DispatchSource](https://developer.apple.com/documentation/dispatch/dispatchsource)를 포함한 많은 디스패치 타입의 기본 클래스이다.
* `enum DispatchPredicate`
    * DispatchPredicate는 dispatchPrecondition(condition:) 메서드를 사용하여 지정된 실행 컨텍스트 내에서 평가할 수 있는 논리적 조건을 나적낸다.
* [Dispatch Constants](https://developer.apple.com/documentation/dispatch/dispatch_constants)
* `func dispatchPrecondition(condition: () -> DispatchPredicate)`
    * 추가 실행에 필요한 디스패치 조건을 확인한다.
* `func activate()`
    * 디스패치 객체를 활성화 한다.
* ~~func dispatch_debugv(DispatchObject, UnsafePointer<Int8>, CVaListPointer)~~
    * **더이상 사용되지 않음**


### Enumerations
* `enum DispatchQueue.GlobalQueuePriority`

### Classes
* `class OS_dispatch_queue_concurrent`
* `class OS_dispatch_queue_global`
* `class OS_dispatch_queue_main`
* `class OS_dispatch_queue_serial`

### Protocols

* `protocol DispatchSourceUserDataReplace`


### Reference
* [Dispatch Enumerations](https://developer.apple.com/documentation/dispatch/dispatch_enumerations)
* [Dispatch Data Types](https://developer.apple.com/documentation/dispatch/dispatch_data_types)


&nbsp;      
&nbsp;      
### [by. 0junChoi](https://github.com/0jun0815) email: <0jun0815@gmail.com>
### [Apple Developer Documentation Dispatch](https://developer.apple.com/documentation/dispatch)
