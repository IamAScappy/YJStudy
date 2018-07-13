# URL Loading System
> 표준 인터넷 프로토콜을 사용하여 URL과 상호 작용하고 서버와 통신한다.


* [Overview](#overview)
* [Topics](#topics)
    * [First Steps](#first-steps)
    * [Requests and Responses](#requests-and-responses)
    * [Cache Behavior](#cache-behavior)
    * [Authentication and Credentials](#authentication-and-credentials)
    * [Cookies](#cookies)
    * [Errors](#errors)
    * [Legacy](#legacy)
* [See Also](#see-also)


&nbsp;    
## Overview
URL 로딩 시스템은 생성한 https 또는 커스텀 프로토콜과 같은 표준 프로토콜을 사용하여 URL로 식별되는 자원에 대한 액세스를 제공한다. 로딩은 비동기적으로 수행되므로 앱이 응답 속도를 유지하며 도착하는 데이터나 오류를 처리할 수 있다.


[URLSession](https://developer.apple.com/documentation/foundation/urlsession) 인스턴스를 사용하여 하나 이상의 [URLSessionTask](https://developer.apple.com/documentation/foundation/urlsessiontask) 인스턴스를 만들면 데이터를 가져와서 애플리케이션에 반환하거나 파일을 다운로드하거나 데이터 및 파일을 원격 위치에 업로드 할 수 있다. 세션을 구성하려면 캐시 및 쿠키를 사용하는 방법, 이동 통신망에서 연결을 허용할지 여부와 같은 동작을 제어하는 [URLSessionConfiguration](https://developer.apple.com/documentation/foundation/urlsessionconfiguration) 객체를 사용한다.


하나의 세션을 반복적으로 사용하여 작업을 만들 수 있다. 예를 들어, 웹 브라우저에는 개인 세션에서 데이터를 캐시하지 않는 일반 및 개인 브라우징 사용을 위한 별도의 세션이 있을 수 있다. 아래 그림은 이러한 구성을 가진 두 세션이 어떻게 여러 작업을 생성하는지 보여준다.


![](https://docs-assets.developer.apple.com/published/4bf9c6d271/6789dd96-afdc-4c18-b8eb-01f9012dc04d.png)


각 세션은 주기적인 업데이트(또는 오류)를 수신하는 델리게이트와 연결된다. 기본 델리게이트는 사용자가 제공한 완료 핸들러 블록을 호출한다. 커스텀 델리게이트를 제공하도록 선택하면 블록이 호출되지 않는다.


백그라운드에서 실행되도록 세션을 구성 할 수 있으므로 앱이 일시 중지된 상태에서 시스템이 대신 데이터를 다운로드하고 앱을 실행하여 결과를 제공 할 수 있다.


&nbsp;      
## Topics
### First Steps
> 세션을 구성 및 작성한 다음 이를 사용하여 URL과 상호 작용하는 작업을 작성한다.

* [Fetching Website Data into Memory](https://developer.apple.com/documentation/foundation/url_loading_system/fetching_website_data_into_memory)
    * URL 세션에서 데이터 작업을 생성하여 데이터를 메모리로 직접 수신한다.
* [Uploading Data to a Website](https://developer.apple.com/documentation/foundation/url_loading_system/uploading_data_to_a_website)
    * 앱의 데이터를 서버에 게시한다.
* [Downloading Files in the Background](https://developer.apple.com/documentation/foundation/url_loading_system/downloading_files_in_the_background)
    * 앱이 비활성 상태인 동안 파일을 다운로드하는 작업을 만든다.
* `class URLSession`
    * 관련된 네트워크 데이터 전송 작업 그룹을 조정하는 객체이다.
* `class URLSessionConfiguration`
    * URL 세션의 동작 및 정책을 정의하는 구성 객체이다.
* `class URLSessionTask`
    * URL 세션에서 수행되는 특정 자원 다운로드와 같은 작업.
    

### Requests and Responses
* `struct URLRequest`
    * 프로토콜 또는 URL 스키마와 무관한 URL 로드 요청이다.
* `class URLResponse`
    * URL 로드 요청에 대한 응답과 연관된 메타 데이터이며 프로토콜 및 URL 스키마와는 별도이다.
* `class HTTPURLResponse`
    * HTTP 프로토콜 URL 로드 요청에 대한 응답과 연관된 메타 데이터이다.
    

### Cache Behavior
* [Accessing Cached Data](https://developer.apple.com/documentation/foundation/url_loading_system/accessing_cached_data)
    * URL 요청이 이전에 캐시된 데이터를 사용하는 방법을 제어한다.
* `class CachedURLResponse`
    * URL 요청에 대한 캐시된 응답이다.
* `class URLCache`
    * URL 요청을 캐시된 응답 객체에 매핑하는 객체이다.
    

### Authentication and Credentials
* [Handling an Authentication Challenge](https://developer.apple.com/documentation/foundation/url_loading_system/handling_an_authentication_challenge)
    * 서버가 URL 요청에 대한 인증을 요구할 때 적절하게 응답한다.
* `class URLAuthenticationChallenge`
    * 클라이언트로부터 인증을 요구하는 서버의 도전.
* `class URLCredential`
    * 자격 증명 타입과 영구 저장소 타입에 특정한 정보로 구성된 인증 자격. 
* `class URLCredentialStorage`
    * 공유 자격 증명 캐시의 관리자.
* `class URLProtectionSpace`
    * 인증이 필요한 서버 또는 서버 영역(일반적으로 realm이라고 함).
 
 
### Cookies
* `class HTTPCookie`
    * HTTP 쿠키의 표현.
* `class HTTPCookieStorage`
    * 쿠키 저장소를 관리하는 컨테이너.
    

### Errors
* `struct URLError`
    * URL 로딩 API가 반환하는 오류 코드이다.
* [URL Loading System Error Info Keys](https://developer.apple.com/documentation/foundation/url_loading_system/url_loading_system_error_info_keys)
    * URL 로딩 API에서 생성된 오류 객체의 사용자 정보 딕셔너리에서 이 키를 인식한다.
    

### Legacy
* [Legacy URL Loading Systems](https://developer.apple.com/documentation/foundation/url_loading_system/legacy_url_loading_systems)
    * 이러한 레거시 객체를 사용하지 않도록 코드를 마이그레이션한다.


&nbsp;
## See Also
### Networking
* [Bonjour](https://developer.apple.com/documentation/foundation/bonjour)


&nbsp;      
&nbsp;      
### [by. 0junChoi](https://github.com/0jun0815) email: <0jun0815@gmail.com>
### [Apple Developer Documentation URL Loading System](https://developer.apple.com/documentation/foundation/url_loading_system#2878017)
