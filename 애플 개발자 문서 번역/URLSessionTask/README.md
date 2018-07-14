# URLSessionTask
> URL 세션에서 수행되는 특정 자원 다운로드와 같은 작업이다.


* [Overview](#overview)
* [Topics](#topics)
    * [Controlling the Task State](#controlling-the-task-state)
    * [Obtaining Task Progress](#obtaining-task-progress)
    * [Obtaining General Task Information](#obtaining-general-task-information)
    * [Scheduling Tasks](#scheduling-tasks)
* [Relationships](#relationships)
* [See Also](#see-also)


&nbsp;    
## Overview
URLSessionTask 클래스는 URL 세션의 작업에 대한 기본 클래스이다. 작업은 항상 세션의 일부이다. [URLSession](https://developer.apple.com/documentation/foundation/urlsession) 인스턴스에서 작업 생성 메서드 중 하나를 호출하여 작업을 만든다. 호출하는 메서드에 따라 작업 타입이 결정된다.
* URLSession의 [dataTask(with:)](https://developer.apple.com/documentation/foundation/urlsession/1411554-datatask) 및 관련 메서드를 사용하여 [URLSessionDataTask](https://developer.apple.com/documentation/foundation/urlsessiondatatask) 인스턴스를 생성한다. 데이터 작업은 자원(리소스)을 요청하고 서버의 응답을 메모리의 하나 이상의 NSData 객체로 반환한다. 기본(default) 세션, 임시(ephemeral) 세션 및 공유(shared) 세션에서 지원되지만 백그라운드 세션에서는 지원되지 않는다.  
* URLSession의 [uploadTask(with:from:)](https://developer.apple.com/documentation/foundation/urlsession/1409763-uploadtask) 및 관련 메서드를 사용하여 [URLSessionUploadTask](https://developer.apple.com/documentation/foundation/urlsessionuploadtask) 인스턴스를 만든다. 업로드 작업은 서버의 응답을 검색하기 전에 데이터를 업로드 할 수 있도록 요청 본문(바디)을 쉽게 제공 할 수 있다는 점을 제외하면 데이터 작업과 같다. 또한 업로드 작업은 백그라운드 세션에서 지원된다.
* URLSession의 [downloadTask(with:)](https://developer.apple.com/documentation/foundation/urlsession/1411482-downloadtask) 및 관련 메서드를 사용하여 [URLSessionDownloadTask](https://developer.apple.com/documentation/foundation/urlsessiondownloadtask) 인스턴스를 만든다. 다운로드 작업은 자원을 디스크의 파일로 직접 다운로드한다. 다운도르 작업은 모든 타입의 세션에서 지원된다.
* URLSession [streamTask(withHostName:port:)](https://developer.apple.com/documentation/foundation/urlsession/1411587-streamtask) 또는 [streamTask(with:)](https://developer.apple.com/documentation/foundation/urlsession/1411545-streamtask)를 사용하여 [URLSessionStreamTask](https://developer.apple.com/documentation/foundation/urlsessionstreamtask) 인스턴스를 만든다. 스트림 작업은 호스트 이름과 포트 또는 네트워크 서비스 객체에서 TCP/IP 연결을 설정한다.


작업을 만든 후에는 [resume()](https://developer.apple.com/documentation/foundation/urlsessiontask/1411121-resume) 메서드를 호출하여 작업을 시작한다. 그런 다음 세션은 요청이 완료되거나 실패 할 때까지 작업에 대한 강한 참조를 유지한다. 앱의 내부 북키핑에 유용하지 않으면 작업에 대한 참조를 유지할 필요가 없다.


> **Note**
>
> 모든 작업 프로퍼티는 키-값 관측을 지원한다.


&nbsp;
## Topics
### Controlling the Task State
> 작업 상태 제어

* `func cancel()`
    * 작업을 취소한다.
* `func resume()`
    * 일시 중단 된 경우 작업을 다시 시작한다.
* `func suspend()`
    * 작업을 일시적으로 일시 중단한다.
* `var state: URLSessionTask.State`
    * 작업의 현재 상태(활성 상태, 일시 중단됨, 취소 중이거나 완료 됨)이다.
* `enum URLSessionTask.State`
    * 작업의 현재 상태를 결정하기위한 상수이다.
* `var priority: Float`
    * 호스트가 작업을 처리하기를 원하는 상대적 우선 순위로 0.0(가장 낮은 우선 순위)과 1.0(가장 높은 우선 순위) 사이의 부동 소수점 값으로 지정된다.
* [URL Session Task Priority](https://developer.apple.com/documentation/foundation/urlsessiontask/url_session_task_priority)
    * [priority](https://developer.apple.com/documentation/foundation/urlsessiontask/1410569-priority) 프로퍼티로 사용되는 호스트에 작업 우선 순위 힌트를 제공하기위한 상수이다.
    

### Obtaining Task Progress
> 작업 진행률 구하기

* `var progress: Progress`
    * 전반적인 작업 진행 상태를 나타낸다.
* `var countOfBytesExpectedToReceive: Int64`
    * 작업이 응답 콘텐츠에서 수신하기를 기대하는 바이트 수이다.
* `var countOfBytesReceived: Int64`
    * 작업이 응답 콘텐츠에서 서버로부터 수신 한 바이트 수.
* `var countOfBytesExpectedToSend: Int64`
    * 요청 콘텐츠에서 작업이 전송하려고하는 바이트 수이다.
* `var countOfBytesSent: Int64`
    * 작업이 요청 콘텐츠에서 서버로 보낸 바이트 수이다.
* `let NSURLSessionTransferSizeUnknown: Int64`
    * 전송의 전체 크기를 결정 할 수 없다.


### Obtaining General Task Information
> 일반적인 작업 정보 구하기

* `var currentRequest: URLRequest?`
    * 현재 작업에서 처리중인 URL 요청 객체이다.
* `var originalRequest: URLRequest?`
    * 작업이 생성 될 때 전달 된 원래 요청 객체이다.
* `var response: URLResponse?`
    * 현재 활성화 된 요청에 대한 서버의 응답이다.
* `var taskDescription: String?`
    * 현재 작업에 대한 앱 제공 설명이다.
* `var taskIdentifier: Int`
    * 지정된 세션 내에서 작업을 고유하게 식별하는 식별자이다.
* `var error: Error?`
    * 작업이 실패한 이유를 나타내는 오류 객체이다.


### Scheduling Tasks
> 작업 예약하기

* `var countOfBytesClientExpectsToReceive: Int64`
    * 클라이언트가 수신하기를 기대하는 바이트 수에 대한 최고 추정치이다.
* `var countOfBytesClientExpectsToSend: Int64`
    * 클라이언트가 전송할 것으로 예상되는 바이트 수에 대한 최고 추정치이다.
* `let NSURLSessionTransferSizeUnknown: Int64`
    * 전송의 전체 크기를 결정할 수 없다.
* `var earliestBeginDate: Date?`
    * 네트워크 로드가 시작되는 가장 빠른 날짜이다.


&nbsp;
## Relationships
### Inherits From
* NSObject


### Conforms To
* CVarArg
* Equatable
* Hashable
* NSCopying
* ProgressReporting


&nbsp;
## See Also
### First Steps
* [Fetching Website Data into Memory](https://developer.apple.com/documentation/foundation/url_loading_system/fetching_website_data_into_memory)
* [Uploading Data to a Website](https://developer.apple.com/documentation/foundation/url_loading_system/uploading_data_to_a_website)
* [Downloading Files in the Background](https://developer.apple.com/documentation/foundation/url_loading_system/downloading_files_in_the_background)
* class URLSession
* class URLSessionConfiguration


&nbsp;      
&nbsp;      
### [by. 0junChoi](https://github.com/0jun0815) email: <0jun0815@gmail.com>
### [Apple Developer Documentation URLSessionTask](https://developer.apple.com/documentation/foundation/urlsessiontask)
