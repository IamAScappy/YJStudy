# URLSessionConfiguration
> URL 세션의 동작 및 정책을 정의하는 구성 객체이다.


* [Overview](#overview)
* [Types of Session Configurations](#types-of-session-configurations)
* [Topics](#topics)
    * [Creating a Session Configuration Object](#creating-a-session-configuration-object)
    * [Setting General Properties](#setting-general-properties)
    * [Setting Cookie Policies](#setting-cookie-policies)
    * [Setting Security Policies](#setting-security-policies)
    * [Setting Caching Policies](#setting-caching-policies)
    * [Supporting Background Transfers](#supporting-background-transfers)
    * [Supporting Custom Protocols](#supporting-custom-protocols)
    * [Supporting Multipath TCP](#supporting-multipath-tcp)
    * [Setting HTTP Policy and Proxy Properties](#setting-http-policy-and-proxy-properties)
    * [Supporting Connectivity Changes](#supporting-connectivity-changes)
    * [Deprecated Methods](#deprecated-methods)
* [Relationships](#relationships)
* [See Also](#see-also)


&nbsp;    
## Overview
URLSessionConfiguration 객체는 [URLSession](https://developer.apple.com/documentation/foundation/urlsession) 객체를 사용하여 데이터를 업로드하고 다운로드 할 때 사용할 동작과 정책을 정의한다. 데이터를 업로드하거나 다운로드 할 때 항상 구성 객체를 만드는 것이 첫 번째 단계이다. 이 객체를 사용하여 시간 제한 값, 캐싱 정책, 연결 요구 사항 및 URLSession 객체와 함께 사용할 다른 타입의 정보를 구성한다.


세션 객체를 초기화하기 전에 URLSessionConfiguration 객체를 적절하게 구성하는 것이 중요하다. 세션 객체는 사용자가 제공한 구성 설정의 복사본을 만들고 해당 설정을 사용하여 세션을 구성한다. 일단 구성되면 세션 객체는 URLSessionConfiguration 객체에 대한 모든 변경 사항을 무시한다. 전송 정책을 수정해야하는 경우 세션 구성 객체를 업데이트하고 이를 사용하여 새 URLSession 객체를 만들어야한다.


> **Note**
>
> 경우에 따라 이 구성에 정의된 정책은 작업에 제공된 [NSURLRequest](https://developer.apple.com/documentation/foundation/nsurlrequest) 객체로 지정된 정책에 의해 재정의될 수 있다. 세션의 정책이 더 제한적이지 않으면 요청 객체에 지정된 모든 정책이 존중된다. 예를 들어 세션 구성에서 셀룰러 네트워킹이 허용되어서는 안된다고 지정하면 [NSURLRequest](https://developer.apple.com/documentation/foundation/nsurlrequest) 객체는 셀룰러 네트워킹을 요청할 수 없다.


구성 객체를 사용하여 세션을 만드는 방법에 대한 자세한 내용은 [URLSession](https://developer.apple.com/documentation/foundation/urlsession)을 참조한다.


&nbsp;
## Types of Session Configurations
URL 세션의 동작 및 기능은 주로 세션을 만드는데 사용되는 구성의 종류에 따라 결정된다.


구성 객체가 없는 싱글톤 공유(shared) 세션은 기본 요청을위한 세션이다. 생성하는 세션만큼 커스텀화 할 수 없지만 요구 사항이 매우 제한적일 경우 좋은 출발점으로 사용된다. shared 클래스 메서드를 호출하여 이 세션에 접근한다. 제한 사항에 대한 자세한 내용은 해당 메서드의 토론을 참조한다.


기본(Default) 세션은 공유 세션과 매우 유사하게 동작하지만(더이상 커스텀화 하지 않는 한) 델리게이트를 사용하여 점진적으로 데이터를 가져올 수 있다. URLSessionConfiguration 클래스에서 기본 메서드를 호출하여 기본 세션 구성을 만들 수 있다.


임시(Ephemeral) 세션은 기본 세션과 비슷하지만 캐시, 쿠기 또는 자격 증명을 디스크에 쓰지는 않는다. URLSessionConfiguration 클래스에서 임시 메서드를 호출하여 임시 세션 구성을 만들 수 있다.


백그라운드 세션을 사용하면 앱이 실행되지 않는 동안 백그라운드에서 콘텐츠 업로드 및 다운로드를 수행할 수 있다. URLSessionConfigruation 클래스에서 backgroundSessionConfiguration(_:) 메서드를 호출하여 백그라운드 세션 구성을 생성할 수 있다.


&nbsp;
## Topics
### Creating a Session Configuration Object
> 세션 구성 객체 만들기

* `class var `default`: URLSessionConfiguration`
    * 기본 세션 구성 객체이다.
* `class var ephemeral: URLSessionConfiguration`
    * 캐시, 쿠키 또는 자격 증명에 영구 저장소를 사용하지 않는 세션 구성이다.
* `class func background(withIdentifier: String) -> URLSessionConfiguration`
    * 백그라운드에서 HTTP 및 HTTPS 업로드 또는 다운로드를 수행 할 수 있도록하는 세션 구성 객체를 만든다.
    

### Setting General Properties
> 일반 프로퍼티 설정하기

* `var identifier: String?`
    * 구성 객체의 백그라운드 섹션 식별자이다.
* `var httpAdditionalHeaders: [AnyHashable : Any]?`
    * 요청과 함께 보낼 추가 헤더 딕셔너리다.
* `var networkServiceType: NSURLRequest.NetworkServiceType`
    * 이 구성을 기반으로 세션 내의 모든 작업에 대한 네트워크 서비스 타입이다.
* `var allowsCellularAccess: Bool` 
    * 연결이 셀룰러 네트워크를 통해 이루어져야하는지 여부를 결정하는 부울 값이다.
* `var timeoutIntervalForRequest: TimeInterval`
    * 추가 데이터를 기다릴 때 사용할 제한 시간 간격(timeout interval)이다.
* `var timeoutIntervalForResource: TimeInterval`
    * 자원 요청이 허용해야하는 최대 시간.
* `var sharedContainerIdentifier: String?`
    * 백그라운드 URL 세션의 파일을 다운로드해야하는 공유 컨테이너의 식별자이다.
* `var waitsForConnectivity: Bool`
    * 세션에서 연결이 사용 가능하게 될 때까지 기다리거나 즉시 실패해야하는지 여부를 나타내는 부울 값이다.
    

### Setting Cookie Policies
> 쿠키 정책 설정

* `var httpCookieAcceptPolicy: HTTPCookie.AcceptPolicy`
    * 쿠키를 수락해야하는 시기를 결정하는 정책 상수이다.
* `var httpShouldSetCookies: Bool`
    * 요청에 쿠키 저장소의 쿠키가 포함되어야하는지 여부를 결정하는 부울 값.
* `var httpCookieStorage: HTTPCookieStorage?`
    * 이 세션 내에서 쿠키를 저장하는 쿠키 저장소이다.
* `class HTTPCookieStorage`
    * 쿠키 저장소를 관리하는 컨테이너.
* `class HTTPCookie`
    * HTTP 쿠키의 표현.
    

### Setting Security Policies
> 보안 정책 설정

* `var tlsMaximumSupportedProtocol: SSLProtocol`
    * 이 세션에서 연결을 만들 때 클라이언트가 요청해야하는 최대 TLS 프로토콜 버전이다.
* `var tlsMinimumSupportedProtocol: SSLProtocol`
    * 프로토콜 협상 중에 받아 들여야하는 최소 TLS 프로토콜이다.
* `var urlCredentialStorage: URLCredentialStorage?`
    * 인증을 위한 자격 증명을 제공하는 자격 증명 저장소이다.
    

### Setting Caching Policies
> 캐싱 정책 설정

* `var urlCache: URLCache?`
    * 세션 내의 요청에 캐시된 응답을 제공하기위한 URL 캐시이다.
* `var requestCachePolicy: NSURLRequest.CachePolicy`
    * 캐시에서 응답을 반환할 시기를 결정하는 미리 정의된 상수이다.
    

### Supporting Background Transfers
> 백그라운드 전송 지원

* `var sessionSendsLaunchEvents: Bool`
    * 전송이 끝나면 백그라운드에서 앱을 다시 시작할지 또는 실행할지를 나타내는 부울 값이다.
* `var isDiscretionary: Bool`
    * 최적의 성능을 위해 백그라운드 작업을 시스템의 재량에 따라 예약 할 수 있는지 여부를 결정하는 부울 값.
* `var shouldUseExtendedBackgroundIdleMode: Bool`
    * 앱이 백그라운드로 이동할 때 TCP 연결을 열어 둘지 여부를 나타내는 부울 값이다.
    

### Supporting Custom Protocols
> 커스텀 프로토콜 지원

* `var protocolClasses: [AnyClass]?`
    * 세션의 요청을 처리하는 추가 프로토콜 서브 클래스의 배열이다.
* `class URLProtocol`
    * 프로토콜 고유의 URL 데이터로드를 처리하는 추상 클래스이다.
    

### Supporting Multipath TCP
> 다중 경로 TCP 지원

* [Improving Network Reliability Using Multipath TCP](https://developer.apple.com/documentation/foundation/urlsessionconfiguration/improving_network_reliability_using_multipath_tcp)
    * iOS 기기에서 사용 가능한 라디오를 사용하여 앱의 네트워크 안정성과 성능을 향상시킨다.
* `var multipathServiceType: URLSessionConfiguration.MultipathServiceType`
    * Wi-Fi 및 셀룰러 인터페이스를 통해 데이터를 전송하기위한 다중 경로 TCP 연결 정책을 지정하는 서비스 타입이다.
* `enum URLSessionConfiguration.MultipathServiceType`
    * 다중 경로 TCP가 사용하는 서비스 타입을 지정하는 상수.
    

### Setting HTTP Policy and Proxy Properties
> HTTP 정책 및 프록시 속성 설정

* `var httpMaximumConnectionsPerHost: Int`
    * 주어진 호스트에 대한 최대 동시 연결 수이다.
* `var httpShouldUsePipelining: Bool`
    * 세션에서 HTTP 파이프 라이닝을 사용해야하는지 여부를 결정하는 부울 값이다.
* `var connectionProxyDictionary: [AnyHashable : Any]?`
    * 이 세션에서 사용할 프록시에 대한 정보가 들어있는 딕셔너리이다.
    

### Supporting Connectivity Changes
> 연결 변경 지원

* `var waitsForConnectivity: Bool`
    * 세션에서 연결이 사용 가능하게 될 때가지 기다리거나 즉시 실패해야하는지 여부를 나타내는 부울 값이다.
    

### Deprecated Methods
> 사용되지 않는 메서드

* *class func backgroundSessionConfiguration(String) -> URLSessionConfiguration*
    * HTTP 및 HTTPS 업로드 또는 다운로드를 백그라운드에서 수행 할 수 있도록하는 세션 구성 객체를 반환한다. **더 이상 사용되지 않음**


&nbsp;
## Relationships
### Inherits From
* NSObject


### Conforms To
* CVarArg
* Equatable
* Hashable
* NSCopying


&nbsp;
## See Also
### First Steps
* [Fetching Website Data into Memory](https://developer.apple.com/documentation/foundation/url_loading_system/fetching_website_data_into_memory)
* [Uploading Data to a Website](https://developer.apple.com/documentation/foundation/url_loading_system/uploading_data_to_a_website)
* [Downloading Files in the Background](https://developer.apple.com/documentation/foundation/url_loading_system/downloading_files_in_the_background)
* class URLSession
* class URLSessionTask


&nbsp;      
&nbsp;      
### [by. 0junChoi](https://github.com/0jun0815) email: <0jun0815@gmail.com>
### [Apple Developer Documentation URLSessionConfiguration](https://developer.apple.com/documentation/foundation/urlsessionconfiguration)
