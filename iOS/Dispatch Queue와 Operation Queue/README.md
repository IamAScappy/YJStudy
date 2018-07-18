# Dispatch Queue와 Operation Queue

* [Dispatch Queue](#dispatch-queue)
* [Operation Queue](#operation-queue)
* [Dispatch Queue vs Opration Queue](#dispatch-queue-vs-opration-queue)


## Dispatch Queue
디스패치 큐의 특징:
* 디스패치 큐는 커스텀 작업을 실행하기 위한 C 기반 매커니즘이다.
* 디스패치 큐는 작업을 순차적 또는 동시에 수행하지만 항상 선입선출로 수행한다.
* 직렬(serial) 디스패치 큐는 한 번에 하나의 작업만 실행하고 해당 작업이 완료되어 디큐되기 전까지 대기하다가 새 작업을 시작한다.
* 동시(concurrency) 디스패치 큐는 이미 시작된 작업이 완료될 때까지 기다리지 않고 가능한 많은 작업을 시작한다.


디스패치 큐의 장점:
* 간단하고 단순한 프로그래밍 인터페이스를 제공한다.
* 자동 및 전체 스레드 풀 관리를 제공한다.
* 스레드 스택이 애플리케이션 메모리에 남아있지 않기 때문에 효율적이다.
* 로드 중에 커널에 트랩되지 않는다.
* 디스패치 큐에 작업을 비동기적으로 전달하면 큐를 교착 상태(데드락)로 만들 수 없다.


[Dispatch Queue](https://github.com/0jun0815/YJStudy/tree/master/애플%20개발자%20문서%20번역/DispatchQueue) 참조


&nbsp;
## Operation Queue
연산 큐의 특징:
* 연산 큐는 동시 디스패치 큐와 동일한 프레임워크이며 NSOperationQueue 클래스에 의해 구현된다.
* 연산 큐는 객체를 우선 순위 및 준비 상태에 따라 실행한다.
* 연산 큐는 항상 작업을 동시에 실행하지만 종속성을 사용하여 필요할 때 순차적으로 실행되도록 할 수 있다.
* 큐에 추가된 후 작업은 명시적으로 취소되거나 해당 작업의 실행이 완료될 때까지 큐에 남아있는다. 


[Operation Queue](https://github.com/0jun0815/YJStudy/tree/master/애플%20개발자%20문서%20번역/OperationQueue) 참조


## Dispatch Queue vs Opration Queue
* 디스패치 큐는 직렬(serial), 동시(concurrency) 선택 가능하지만 연산 큐는 항상 동시 실행된다.
* 디스패치 큐는 작업을 선입선출로 수행하지만 연산 큐는 종속성을 통해 순서를 특정 순서로 수행할 수 있다.


[Concurrency Programming Guide](https://github.com/0jun0815/YJStudy/tree/master/애플%20개발자%20문서%20번역/Concurrency%20Programming%20Guide#the-move-away-from-threads) 참조


&nbsp;
&nbsp;      
### [by. 0junChoi](https://github.com/0jun0815) email: <0jun0815@gmail.com>
