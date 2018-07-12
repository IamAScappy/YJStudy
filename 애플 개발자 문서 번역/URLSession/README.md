# URLSession
> 관련된 네트워크 데이터 전송 작업 그룹을 조정하는 객체이다.


* [Overview](#overview)
* [Types of URL Sessions](#types-of-url-sessions)
* [Types of URL Session Tasks](#types-of-url-session-tasks)
* [Using a Session Delegate](#using-a-session-delegate)
* [Asynchronicity and URL Sessions](#asynchronicity-and-url-sessions)
* [Protocol Support](#protocol-support)
* [App Transport Security (ATS)](#app-transport-security-(ats))
* [Using a URL Session](#using-a-url-session)
* [NSCopying Behavior](#nscopying-behavior)
* [Thread Safety](#thread-safety)
* [Topics](#topics)
    * [Creating a Session](#creating-a-session)
    * [Configuring a Session](#configuring-a-session)
    * [Adding Data Tasks to a Session](#adding-data-tasks-to-a-session)
    * [Adding Download Tasks to a Session](#adding-download-tasks-to-a-session)
    * [Adding Upload Tasks to a Session](#adding-upload-tasks-to-a-session)
    * [Adding Stream Tasks to a Session](#adding-stream-tasks-to-a-session)
    * [Managing the Session](#managing-the-session)
    * [Handling Errors](#handling-errors)
* [Relationships](#relationships)
* [See Also](#see-also)


&nbsp;    
## Overview
URLSession 클래스 및 관련 클래스는 콘텐츠를 다운로드하기위한 API를 제공한다. 이 API는 인증 지원을위한 다양한 델리게이트 메서드를 제공하며 앱이 실행되지 않을 때 백그라운드 다운로드를 수행할 수 있도록하거나 iOS에서는 앱이 일시 중지된 상태에서 앱을 다운로드하는 기능을 제공한다.


& nbsp;
>  **Important**
> 
> URLSession API에는 참조 문서를 독자적으로 읽는 경우 명확하지 않을 수 있는 여러 가지 클래스가 상당히 복잡한 방식으로 함께 포함된다. 이 API를 사용하기 전에 [URL Loading System](https://developer.apple.com/documentation/foundation/url_loading_system) 개요를 읽어야한다. [First Steps](https://developer.apple.com/documentation/foundation/url_loading_system#2878017) 섹션의 기사에서는 URLSession을 사용하여 일반적인 작업을 수행하는 예제를 제공한다. 


&nbsp;
URLSession API를 사용하면 하나 이상의 세션이 만들어지며 각 세션은 관련 데이터 전송 작업 그룹을 조정한다. 예를 들어 웹 브라우저를 만드는 경우 탭 또는 창당 하나의 세션을 만들거나 대화형으로 사용할 세션을 만들고 백그라운드 다운로드용으로 세션을 만들 수 있다. 각 세션 내에서 앱은 일련의 작업을 추가한다. 각 작업은 특정 URL에 대한 요청을 나타낸다(필요한 경우 HTTP 리다이렉션에 따라).


&nbsp;
## Types of URL Sessions
지정된 URL 세션 내의 작업은 단일 세션 호스트의 최대 동시 연결 수, 셀룰러 네트워크를 통한 연결 허용 여부 등과 같은 연결 동작을 정의하는 공통 세션 구성 객체를 공유한다.


URLSession에는 기본 요청에 대한 [shared](https://developer.apple.com/documentation/foundation/urlsession/1409000-shared) 세션(구성 객체가 없음)이 있다. 생성하는 세션만큼 커스텀화 할 수는 없지만 요구 사항이 매우 제한적일 경우 좋은 시작점으로 사용된다. 공유 클래스 메서드를 호출하여 이 세션에 액세스한다. 다른 종류의 세션의 경우 다음 세 가지 구성 중 하나로 [URLSession](https://developer.apple.com/documentation/foundation/urlsession)을 인스턴스화한다:
* 기본(default) 세션은 공유(shared) 세션과 매우 유사하게 작동하지만 더 많은 구성을 허용하며 데이터를 델리게이트와 점진적으로 얻을 수 있다.
* 임시(ephemeral) 세션은 공유 세션과 유사하지만 캐시, 쿠키 또는 자격 증명을 디스크에 쓰면 안된다.
* 백그라운드(background) 세션을 사용하면 앱이 실행되지 않는 동안 백그라운드에서 콘텐츠 업로드 및 다운로드를 수행 할 수 있다.


각 타입의 구성을 만드는 방법에 대한 자세한 내용은 [URLSessionConfiguration](https://developer.apple.com/documentation/foundation/urlsessionconfiguration) 클래스의 [Creating a Session configuration Object](https://developer.apple.com/documentation/foundation/urlsessionconfiguration#1660412)를 참조한다.


&nbsp;
## Types of URL Session Tasks
세션에서 선택적으로 데이터를 서버에 업로드한 다음 디스크의 파일 또는 메모리의 하나 이상의 NSData 객체로 서버에서 데이터를 검색하는 작업을 만든다. URLSession API는 세 가지 타입의 작업(task)를 제공한다:
* 데이터 작업은 NSData 객체를 사용하여 데이터를 보내고 받는다. 데이터 작업은 서버에 대한 짧고 종종 상호 작용하는 요청을 위한 것이다.
* 업로드 작업은 데이터 작업과 비슷하지만 데이터를 파일 형태로 보내고 앱이 실행되지 않는 동안 백그라운드 업로드를 지원한다.
* 다운로드 작업은 파일 형태로 데이터를 검색하고 앱이 실행되지 않는 동안 백그라운드 다운로드 및 업로드를 지원한다.


&nbsp;
## Using a Session Delegate
또한 세션의 작업은 공통된 델리게이트를 공유하므로 인증 실패, 데이터가 서버에서 도착한 시점, 데이터를 캐시 할 준비가 된 시점 등 다양한 이벤트가 발생할 때 정보를 제공하고 얻을 수 있다. [Using a URL Session](https://developer.apple.com/documentation/foundation/urlsession#2926844)에는 세션이 작업을 수행할 때 발생하는 이벤트 및 결과로 호출되는 델리게이트 메서드의 단계별 목록이 있다.


반면에 델리게이트가 제공하는 기능이 필요하지 않은 경우 세션을 만들 때 nil을 전달하여 제공하지 않고 이 API를 사용할 수 있다.


&nbsp;
> **Important**
>
> 세션 객체는 앱이 종료되거나 명시적으로 세션을 무효화 할 때까지 델리게이트에 대한 강한 참조를 유지한다. 세션을 무효화하지 않으면 앱이 종료될 때까지 메모리가 누수된다.


&nbsp;
## Asynchronicity and URL Sessions
대부분의 네트워킹 API와 마찬가지로 URLSession API는 매우 비동기적이다. 호출하는 메서드에 따라 다음 두 가지 방법 중 하나로 애플리케이션에 데이터를 반환한다:
* 전송이 성공적으로 완료되거나 오류가 발생하면 완료 핸들러 블록을 호출한다.
* 데이터가 수신되고 전송이 완료되면 세션의 델리게이트에서 메서드를 호출한다.


URLSession API는 이 정보를 델리게이트에게 전달하는 것 외에도 상태의 진행 상태를 기반으로 프로그래밍 방식으로 의사 결정을 내릴 필요가 있을 때 쿼리 할 수 있는 state 및 progess 프로퍼티를 제공한다(상태가 언제든 변경 될 수 있음).


또한 URL 세션은 작업 취소, 다시 시작 또는 재시작 및 일시 중지를 지원하고 중단된 위치에서 일시 중지, 취소 또는 실패한 다운로드를 다시 시작할 수 있는 기능을 제공한다.


&nbsp;
## Protocol Support
URLSession 클래스는 기본적으로 사용자 시스템 환경 설정에 구성된대로 프록시 서버 및 SOCKS 게이트웨이에 대한 투명한 지원을 통해 데이터, 파일, FTP, HTTP 및 HTTPS URL 스키마를 지원한다.


URLSession은 HTTP/1.1, SPDY, HTTP/2 프로토콜을 지원한다. HTTP/2 지원은 [RFC 7540](https://tools.ietf.org/html/rfc7540)에 설명되어 있으며 프로토콜 협상을 위해 ALPN 또는 NPN을 지원하는 서버가 필요하다.


또한 URLProtocol을 서브 클래스화하여 자신의 커스텀 네트워킹 프로토콜 및 URL 스키마(앱의 비공개 용도)를 지원할 수 있다.


&nbsp;
## App Transport Security (ATS)
iOS 9.0과 OS X 10.11부터는 ATS(App Transport Security)라는 새로운 보안 기능이 기본적으로 URLSession으로 만들어진 모든 HTTP 연결에 사용된다. ATS는 HTTP 연결이 HTTPS (RFC 2818)를 사용해야 함을 요구한다.


자세한 내용은 [Information Property List Key Reference](https://developer.apple.com/library/archive/documentation/General/Reference/InfoPlistKeyReference/Introduction/Introduction.html#//apple_ref/doc/uid/TP40009247)에서 [NSAppTransportSecurity](https://developer.apple.com/library/archive/documentation/General/Reference/InfoPlistKeyReference/Articles/CocoaKeys.html#//apple_ref/doc/uid/TP40009251-SW33)를 참조한다.


&nbsp;      
## Using a URL Session
URLSession 클래스를 사용하여 요청하려면 다음을 수행한다:
1. 세션 구성을 만든다. 백그라운드 세션의 경우 이 구성에는 고유 식별자가 있어야한다. 해당 식별자를 저장하고 앱이 충돌하거나 종료되거나 일시 중지된 경우 식별자를 사용하여 세션과 다시 연결한다.
2. 구성 객체 및 선택적으로 델리게이트를 지정하여 세션을 만든다.
3. 각각의 리소스 요청을 나타내는 세션 내에서 작업(task) 객체를 만든다. 이러한 작업 객체는 달성하려는 동작에 따라 [URLSessionTask](https://developer.apple.com/documentation/foundation/urlsessiontask)-[URLSessionDataTask](https://developer.apple.com/documentation/foundation/urlsessiondatatask), [URLSessionUploadTask](https://developer.apple.com/documentation/foundation/urlsessionuploadtask) 또는 [URLSessionDownloadTask](https://developer.apple.com/documentation/foundation/urlsessiondownloadtask)의 서브 클래스이다. 각 작업은 일시 중지된 상태에서 시작된다. 앱에서 작업에 대한 [resume()](https://developer.apple.com/documentation/foundation/urlsessiontask/1411121-resume)을 호출하면 지정된 리소스를 다운로드하기 시작한다.


작업을 시작하면 세션에서 다음과 같은 델리게이트 메서드를 호출한다:
1. 서버와의 초기 핸드 셰이크에 SSL 클라이언트 인증서와 같은 연결 수준 시도가 필요한 경우 URLSession은 [urlSession(_:task:didReceive:completionHandler:)](https://developer.apple.com/documentation/foundation/urlsessiontaskdelegate/1411595-urlsession) 또는 [urlSession(_:didReceive:completionHandler:)](https://developer.apple.com/documentation/foundation/urlsessiondelegate/1409308-urlsession) 델리게이트 메서드를 호출한다.
2. 작업의 데이터가 스트림에서 제공되는 경우 URLSession 객체는 델리게이트의 [urlSession(_:task:needNewBodyStream:)](https://developer.apple.com/documentation/foundation/urlsessiontaskdelegate/1410001-urlsession) 델리게이트 메서드를 호출하여 새 요청의 본문 데이터를 제공하는 [InputStream](https://developer.apple.com/documentation/foundation/inputstream)의 인스턴스를 가져온다.
3. 본문(body) 콘텐츠를 서버에 처음 업로드하는 경우 (해당되는 경우) 델리게이트는 업로드 진행률을 보고하는 [urlSession(_:task:didSendBodyData:totalBytesSent:totalBytesExpectedToSend:)](https://developer.apple.com/documentation/foundation/urlsessiontaskdelegate/1408299-urlsession) 콜백을 주기적으로 받는다.
4. 서버가 응답을 보낸다.
5. 응답에서 인증이 필요함을 나타내면 세션에서 델리게이트의 [urlSession(_:task:didReceive:completionHandler:)](https://developer.apple.com/documentation/foundation/urlsessiontaskdelegate/1411595-urlsession) 메서드를 호출한다. 2단계로 돌아간다.
6. 응답이 HTTP 리다이렉션 응답인 경우 URLSession 객체는 델리게이트의 [urlSession(_:task:willPerformHTTPRedirection:newRequest:completionHandler:)](https://developer.apple.com/documentation/foundation/urlsessiontaskdelegate/1411626-urlsession) 메서드를 호출한다. 이 델리게이트 메서드는 제공된 완료 핸들러를 제공된 [NSURLRequest](https://developer.apple.com/documentation/foundation/nsurlrequest) 객체(리다이렉션을 따르는), 새 NSURLRequest 객체(다른 URL로 리 다이렉션) 또는 nil(리다이렉션의 응답 본문(body)를 유효한 응답으로 처리하여 결과로 반환)와 같이 호출한다.
    * 리다이렉션을 따르기로 결정한 경우 2단계로 돌아간다.
    * 델리게이트가 이 메서드를 구현하지 않으면 최대 리다이렉션 수까지 리다이렉션이 이어진다.
7. [downloadTask(withResumeData:)](https://developer.apple.com/documentation/foundation/urlsession/1409226-downloadtask) 또는 [downloadTask(withResumeData:completionHandler:)](https://developer.apple.com/documentation/foundation/urlsession/1411598-downloadtask)를 호출하여 만든 다운로드 (또는 리다운로드) 작업의 경우 URLSession은 새 작업 객체로 델리게이트의 [urlSession(_:downloadTask:didResumeAtOffset:expectedTotalBytes:)](https://developer.apple.com/documentation/foundation/urlsessiondownloaddelegate/1408142-urlsession) 메서드를 호출한다.
8. 데이터 작업의 경우 URLSession 객체는 델리게이트의 [urlSession(_:dataTask:didReceive:completionHandler:)](https://developer.apple.com/documentation/foundation/urlsessiondatadelegate/1410027-urlsession) 메서드를 호출한다. 데이터 작업을 다운로드 작업으로 변환할 것인지 여부를 결정한 다음 완료 핸들러를 호출하여 작업을 변환, 계속 또는 취소한다. 앱이 데이터 작업을 다운로드 작업으로 변환하도록 선택한 경우 URLSession은 새 다운로드 작업을 매개 변수로 사용하여 델리게이트의 [urlSession(_:dataTask:didBecome:)](https://developer.apple.com/documentation/foundation/urlsessiondatadelegate/1409936-urlsession) 메서드를 호출한다. 이 호출 후 델리게이트는 데이터 작업에서 더이상 콜백을 받지 않고 다운로드 작업에서 콜백을 수신하기 시작한다.
9. 서버에서 전송하는 동안 델리게이트는 주기적으로 작업 수준 콜백을 수신하여 전송 진행 상황을 보고한다. 데이터 작업의 경우, 세션은 수신된 실제 데이터를 사용하여 델리게이트의 [urlSession(_:dataTask:didReceive:)](https://developer.apple.com/documentation/foundation/urlsessiondatadelegate/1411528-urlsession) 메서드를 호출한다. 다운로드 작업의 경우 세션은 델리게이트의 urlSession(_:downloadTask:didWriteData:totalBytesWritten:totalBytesExpectedToWrite:)](https://developer.apple.com/documentation/foundation/urlsessiondownloaddelegate/1409408-urlsession) 메서드를 디스크에 성공적으로 기록된 바이트 수와 함께 호출한다. 사용자가 다운로드를 일시 중지하도록 앱에 지시하면 [cancel(byProducingResumeData:)](https://developer.apple.com/documentation/foundation/urlsessiondownloadtask/1411634-cancel) 메서드를 호출하여 작업을 취소한다. 나중에 사용자가 다운로드를 재개하도록 앱에 요청하면 반환된 반환된 다시 시작 데이터를 [downloadTask(withResumeData:)](https://developer.apple.com/documentation/foundation/urlsession/1409226-downloadtask) 또는 [downloadTask(withResumeData:completionHandler:)](https://developer.apple.com/documentation/foundation/urlsession/1411598-downloadtask) 메서드로 전달하여 다운로드를 계속하는 새 다운로드 작업을 만든다. (1단계로 이동한다.)
10. 데이터 작업의 경우, URLSession 객체는 델리게이트의 [urlSession(_:dataTask:willCacheResponse:completionHandler:)](https://developer.apple.com/documentation/foundation/urlsessiondatadelegate/1411612-urlsession) 메서드를 호출할 수 있다. 앱은 캐싱을 허용할지 여부를 결정해야 한다. 이 메서드를 구현하지 않으면 기본적으로 세션의 구성 객체에 지정된 캐싱 정책을 사용한다.
11. 응답이 여러 부분으로 인코딩 된 경우 세션에서 델리게이트의 didReceiveResponse 메서드를 다시 호출한 다음 0개 이상의 추가 didReceiveData를 호출할 수 있다. 이 경우 8단계(didReceiveResponse 호출 처리)로 이동한다.
12. 다운로드 작업이 성공적으로 완료되면 URLSession 객체는 임시 파일의 위치를 사용하여 작업의 [urlSession(_:downloadTask:didFinishDownloadingTo:)](https://developer.apple.com/documentation/foundation/urlsessiondownloaddelegate/1411575-urlsession) 메서드를 호출한다. 이 델리게이트 메서드가 반환되기 전에 앱에서 이 파일의 응답 데이터를 읽거나 영구 위치로 이동해야 한다.
13. 모든 작업이 완료되면 URLSession 객체는 오류 객체 또는 nil(작업이 성공적으로 완료된 경우)과 함께 델리게이트의 [urlSession(_:task:didCompleteWithError:)](https://developer.apple.com/documentation/foundation/urlsessiontaskdelegate/1411610-urlsession) 메서드를 호출한다. 다운로드 작업을 다시 시작할 수 있으면 NSError 객체의 userInfo 딕셔너리에 [NSURLSessionDownloadTaskResumeData](https://developer.apple.com/documentation/foundation/nsurlsessiondownloadtaskresumedata) 키 값이 들어 있다. 애플리케이션은 이 값을 전달하여 [downloadTask(withResumeData:)](https://developer.apple.com/documentation/foundation/urlsession/1409226-downloadtask) 또는 [downloadTask(withResumeData:completionHandler:)](https://developer.apple.com/documentation/foundation/urlsession/1411598-downloadtask)를 호출하여 기존 다운로드를 계속하는 새 다운로드 작업을 만든다. 작업을 다시 시작할 수 없는 경우 앱에서 새 다운로드 작업을 만들고 처음부터 트랜잭션을 다시 시작해야 한다. 두 경우 모두 서버 오류가 아닌 다른 이유로 전송이 실패하면 3단계(작업 객체 작성 및 재개)로 이동한다. 
14. 더 이상 세션이 필요 없으면 [invalidateAndCancel()](https://developer.apple.com/documentation/foundation/urlsession/1411538-invalidateandcancel)(미해결 작업 취소) 또는 [finishTasksAndInvalidate()](https://developer.apple.com/documentation/foundation/urlsession/1407428-finishtasksandinvalidate)(미해결 작업이 객체를 무효화하기 전에 완료되도록)를 호출하여 세션을 무효화 할 수 있다. 세션을 무효화하지 않으면 앱이 종료될 때 자동으로 사라진다(활성 작업이 있는 백그라운드 세션이 아닌 경우). 세션을 무효화 한 후 모든 미해결 된 작업이 취소되거나 완료되면 세션에서 델리게이트의 [urlSession(_:didBecomeInvalidWithError:)](https://developer.apple.com/documentation/foundation/urlsessiondelegate/1407776-urlsession) 메서드를 호출한다. 해당 델리게이트 메서드가 반환되면 세션에서 해당 델리게이트에 대한 강력한 참조를 삭제한다.


앱이 진행중인 다운로드를 취소하면 URLSession 객체는 오류가 발생한 것처럼 델리게이트의 [urlSession(_:task:didCompleteWithError:)](https://developer.apple.com/documentation/foundation/urlsessiontaskdelegate/1411610-urlsession) 메서드를 호출한다.


&nbsp;
## NSCopying Behavior
세션 및 작업 객체는 다음과 같이 [NSCopying](https://developer.apple.com/documentation/foundation/nscopying) 프로토콜을 준수한다.
* 애플리케이션에서 세션 또는 작업 객체를 복사하면 동일한 객체가 다시 반환된다.
* 애플리케이션에서 구성 객체를 복사하면 독자적으로 수정할 수 있는 새 복사본이 생성된다.


&nbsp;
## Thread Safety
URL 세션 API 자체는 완전한 스레드 안전성을 제공한다. 모든 스레드 컨텍스트에서 세션 및 작업을 자유롭게 만들 수 있으며 델리게이트 메서드가 제공된 완료 핸들러를 호출하면 작업이 올바른 델리게이트 큐에 자동으로 예약된다.


&nbsp;
> **Warning**
>
> [urlSessionDidFinishEvents(forBackgroundURLSession:)](https://developer.apple.com/documentation/foundation/urlsessiondelegate/1617185-urlsessiondidfinishevents) 세션 델리게이트 메서드가 보조 스레드에서 호출 될 수 있다. 그러나 iOS에서 해당 메서드를 구현하면 [application(_:handleEventsForBackgroundURLSession:completionHandler:)](https://developer.apple.com/documentation/uikit/uiapplicationdelegate/1622941-application) 애플리케이션 델리게이트 메서드에서 제공되는 완료 핸들러를 호출해야 할 수 있다. 메인 스레드에서 완료 핸들러를 호출 해야 한다.


&nbsp;      
## Topics
### Creating a Session
> 세션 생성

* `init(configuration: URLSessionConfiguration)`
    * 지정된 세션 구성으로 세션을 만든다.
* `init(configuration: URLSessionConfiguration, delegate: URLSessionDelegate?, delegateQueue: OperationQueue?)`
    * 지정된 세션 구성, 델리게이트 및 연산 큐를 사용하여 세션을 만든다.
* `class var shared: URLSession`
    * 공유 싱글턴 섹션 객체
    

### Configuring a Session
> 세션 구성

* `var configuration: URLSessionConfiguration`
    * 이 세션의 구성 객체 복사본이다.
* `var delegate: URLSessionDelegate?`
    * 이 객체가 만들어 질 때 할당 된 델리게이트이다.
* `protocol URLSessionDelegate`
    * URLSession 인스턴스가 델리게이트에서 세션 생명 주기 변경과 같은 세션 수준 이벤트를 처리하도록 호출하는 메서드를 정의하는 프로토콜이다.
* `protocol URLSessionTaskDelegate`
    * URLSession 인스턴스가 델리게이트에서 작업 레벨 이벤트를 처리하도록 호출하는 메서드를 정의하는 프로토콜이다.
* `var delegateQueue: OperationQueue`
    * 이 객체가 생성 될 때 제공된 연산 큐이다.
* `var sessionDescription: String?`
    * 세션에 대한 앱 정의 설명 레이블이다.
    

### Adding Data Tasks to a Session
> 세션에 데이터 작업 추가

* `func dataTask(with: URL) -> URLSessionDataTask`
    * 지정된 URL의 콘텐츠를 검색하는 작업을 만든다.
* `func dataTask(with: URL, completionHandler: (Data?, URLResponse?, Error?) -> Void) -> URLSessionDataTask`
    * 지정된 URL의 콘텐츠를 검색하고 완료되면 핸들러를 호출하는 작업을 만든다.
* `func dataTask(with: URLRequest) -> URLSessionDataTask`   
    * 지정된 URL 요청 객체를 기반으로 URL의 콘텐츠를 검색하는 작업을 만든다.
* `func dataTask(with: URLRequest, completionHandler: (Data?, URLResponse?, Error?) -> Void) -> URLSessionDataTask`
    * 지정된 URL 요청 객체를 기반으로 URL의 콘텐츠를 검색하고 완료시 핸들러를 호출하는 작업을 만든다.
* `class URLSessionDataTask`
    * 다운로드한 데이터를 메모리에있는 앱으로 직접 반환하는 URL 세션 작업.
* `protocol URLSessionDataDelegate`
    * URLSession 인스턴스가 델리게이트를 호출하여 데이터 관련 작업 수준 이벤트를 처리하고 작업을 업롣하는 메서드를 정의하는 프로토콜이다.
    

### Adding Download Tasks to a Session
> 세션에 다운로드 작업 추가

* `func downloadTask(with: URL) -> URLSessionDownloadTask`
    * 지정된 URL의 콘텐츠를 검색하고 결과를 파일에 저장하는 다운로드 작업을 만든다.
* `func downloadTask(with: URL, completionHandler: (URL?, URLResponse?, Error?) -> Void) -> URLSessionDownloadTask`
    * 지정된 URL의 콘텐츠를 검색하고 결과를 파일에 저장하고 완료시 핸들러를 호출하는 다운로드 작업을 만든다.
* `func downloadTask(with: URLRequest) -> URLSessionDownloadTask`
    * 지정된 URL의 요청 객체를 기반으로 URL의 콘텐츠를 검색하고 결과를 파일에 저장하는 다운로드 작업을 만든다.
* `func downloadTask(with: URLRequest, completionHandler: (URL?, URLResponse?, Error?) -> Void) -> URLSessionDownloadTask`
    * 지정된 URL 요청 객체를 기반으로 URL 콘텐츠를 검색하고 결과를 파일에 저장하며 완료시 핸들러를 호출하는 다운로드 작업을 만든다.
* `func downloadTask(withResumeData: Data) -> URLSessionDownloadTask`
    * 이전에 취소되었거나 실패한 다운로드를 다시 시작하기위한 다운로드 작업을 만든다.
* `func downloadTask(withResumeData: Data, completionHandler: (URL?, URLResponse?, Error?) -> Void) -> URLSessionDownloadTask`
    * 이전에 취소되었거나 실패한 다운로드를 다시 시작하기위한 다운로드 작업을 만들고 완료되면 핸들러를 호출한다.
* `class URLSessionDownloadTask`
    * 다운로드한 데이터를 파일에 저장하는 URL 세션 작업.
* `protocol URLSessionDownloadDelegate`
    * URLSession 인스턴스가 델리게이트에서 다운로드 작업과 관련된 작업 수준 이벤트를 처리하기 위해 호출하는 메서드를 정의하는 프로토콜이다.
    

### Adding Upload Tasks to a Session
> 세션에 업로드 작업 추가

* `func uploadTask(with: URLRequest, from: Data) -> URLSessionUploadTask`
    * 지정된 URL 요청 객체에 대한 HTTP 요청을 수행하고 제공된 데이터를 업로드하는 작업을 만든다.
* `func uploadTask(with: URLRequest, from: Data?, completionHandler: (Data?, URLResponse?, Error?) -> Void) -> URLSessionUploadTask`
    * 지정된 URL 요청 객체에 대한 HTTP 요청을 수행하고 제공된 데이터를 업로드하고 완료시 핸들러를 호출하는 작업을 만든다.
* `func uploadTask(with: URLRequest, fromFile: URL) -> URLSessionUploadTask`
    * 지정된 파일을 업로드하기위한 HTTP 요청을 수행하는 작업을 만든다.
* `func uploadTask(with: URLRequest, fromFile: URL, completionHandler: (Data?, URLResponse?, Error?) -> Void) -> URLSessionUploadTask`
    * 지정된 파일을 업로드하기위한 HTTP 요청을 수행한 후 작업이 완료되면 핸들러를 호출하는 작업을 만든다.
* `func uploadTask(withStreamedRequest: URLRequest) -> URLSessionUploadTask`
    * 지정된 URL 요청을 기반으로 데이터를 업로드하기위한 HTTP 요청을 수행하는 작업을 만든다.
* `class URLSessionUploadTask`
    * 요청 본문(body)의 네트워크에 데이터를 업로드하는 URL 세션 작업.
* `protocol URLSessionDataDelegate`
    * URLSession 인스턴스가 델리게이트를 호출하여 데이터 관련 작업 수준 이벤트를 처리하고 작업을 업로드하는 메서드를 정의하는 프로토콜이다.
    

### Adding Stream Tasks to a Session
> 세션에 스트림 작업 추가

* `func streamTask(withHostName: String, port: Int) -> URLSessionStreamTask`
    * 지정된 호스트 이름 및 포트에 대해 양방향 TCP/IP 연결을 설정하는 작업을 만든다.
* `func streamTask(with: NetService) -> URLSessionStreamTask`
    * 지정된 네트워크 서비스를 사용하여 양방향 TCP/IP 연결을 설정하는 작업을 만든다.
* `class URLSessionStreamTask`
    * 스트림 기반 URL 세션 작업이다.
* `protocol URLSessionStreamDelegate`
    * URLSession 인스턴스가 델리게이트에서 스트림 작업과 관련된 작업 수준 이벤트를 처리하기 위해 호출하는 메서드를 정의하는 프로토콜이다.


### Managing the Session
> 세션 관리

* `func finishTasksAndInvalidate()`
    * 세션을 무효화하여 완료되지 않은 작업을 완료 할 수 있게한다.
* `func flush(completionHandler: () -> Void)`
    * 쿠키 및 자격 증명을 디스크로 플러시하고 임시 캐시를 지우고 새로운 TCP 연결에서 향후 요청이 발생하는지 확인한다.
* `func getTasksWithCompletionHandler(([URLSessionDataTask], [URLSessionUploadTask], [URLSessionDownloadTask]) -> Void)`
    * 세션의 모든 데이터, 업로드 및 다운로드 작업으로 완료 콜백을 비동기적으로 호출한다.
* `func getAllTasks(completionHandler: ([URLSessionTask]) -> Void)`
    * 세션의 모든 작업으로 완료 콜백을 비동기적으로 호출한다.
* `func invalidateAndCancel()`
    * 모든 미해결된 작업을 취소한 다음 세션을 무효화한다.
* `func reset(completionHandler: () -> Void)`
    * 모든 쿠키, 캐시 및 자격 증명 저장소를 비운다. 디스크 파일을 제거하고 진행중인 다운로드를 디스크에 플러시하고 향후 요청이 새소켓에서 발생하는지 확인한다.
    
    
### Handling Errors
> 오류 처리

* `NSURLSession-Specific NSError userInfo Dictionary Keys`
    * NSURLSession API에 의해 반환된 NSError 객체와 함께 사용되는 키이다.
* `Background Task Cancellation Reasons`
    * 백그라운드 태스크가 취소된 이유를 나타내는 상수이다.


&nbsp;
## Relationships
### Inherits From
* NSObject


### Conforms To
* CVarArg, Equatable, Hashable


&nbsp;
## See Also
### First Steps
* [Fetching Website Data into Memory](https://developer.apple.com/documentation/foundation/url_loading_system/fetching_website_data_into_memory)
* [Uploading Data to a Website](https://developer.apple.com/documentation/foundation/url_loading_system/uploading_data_to_a_website)
* [Downloading Files in the Background](https://developer.apple.com/documentation/foundation/url_loading_system/downloading_files_in_the_background)
* class URLSessionConfiguration
* class URLSessionTask


&nbsp;      
&nbsp;      
### [by. 0junChoi](https://github.com/0jun0815) email: <0jun0815@gmail.com>
### [Apple Developer Documentation URLSession](https://developer.apple.com/documentation/foundation/urlsession)
