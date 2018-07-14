# URLResponse
> URL 로드 요청에 대한 응답과 연관된 메타 데이터이며 프로토콜 및 URL 스키마와는 별도이다.


* [Overview](#overview)
* [Topics](#topics)
    * [Creating a Response](#creating-a-response)
    * [Getting the Response Properties](#getting-the-response-properties)
* [Relationships](#relationships)
* [See Also](#see-also)


&nbsp;    
## Overview
관련 [HTTPURLResponse](https://developer.apple.com/documentation/foundation/httpurlresponse) 클래스는 HTTP URL 로드 요청에 대한 응답을 나타내는 객체가 있고 응답 헤더와 같은 추가 프로토콜 관련 정보를 저장하는 URLResponse의 일반적으로 사용되는 하위 클래스이다. HTTP 요청을 할 때마다 돌아오는 URLResponse 객체는 실제로 [HTTPURLResponse](https://developer.apple.com/documentation/foundation/httpurlresponse) 클래스의 인스턴스이다.


> **Note**
>
> URLResponse 객체는 URL 콘텐츠를 나타내는 실제 바이트를 포함하지 않는다. 대신 데이터는 요청을 시작하는데 사용 된 메서드 및 클래스에 따라 요청이 완료 될 때 델리게이트 호출을 통해 한 번에 하나씩 또는 전체적으로 반환된다.
>
> [Fetching Website Data into Memory](https://developer.apple.com/documentation/foundation/url_loading_system/fetching_website_data_into_memory)를 읽고 URL 로드에서 콘텐츠 데이터를 수신하는 다양한 방법을 학습한다.


&nbsp;
## Topics
### Creating a Response
> 응답 만들기

* `init(url: URL, mimeType: String?, expectedContentLength: Int, textEncodingName: String?)`
    * 지정된 값으로 설정된 URL, MIME 타입, 길이 및 텍스트 인코딩을 사용하여 초기화된 URLResponse 객체를 만든다.
    

### Getting the Response Properties
> 응답 프로퍼티 가져오기

* `var expectedContentLength: Int64`
    * 응답 콘텐츠의 예상 길이이다.
* `var suggestedFilename: String?`
    * 응답 데이터의 제안 파일 이름이다.
* `var mimeType: String?`
    * 응답의 MIME 타입이다.
* `var textEncodingName: String?`
    * 응답의 원래 소스에서 제공 한 텍스트 인코딩의 이름이다.
* `var url: URL?`
    * 응답의 URL이다.


&nbsp;
## Relationships
### Inherits From
* NSObject


### Conforms To
* CVarArg
* Equatable
* Hashable
* NSCopying
* NSSecureCoding


&nbsp;
## See Also
### Request and Responses
* struct URLRequest
* class HTTPURLResponse


&nbsp;      
&nbsp;      
### [by. 0junChoi](https://github.com/0jun0815) email: <0jun0815@gmail.com>
### [Apple Developer Documentation URLResponse](https://developer.apple.com/documentation/foundation/urlresponse)
