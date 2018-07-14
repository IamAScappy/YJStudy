# URLSessionDataTask
> 다운로드 한 데이터를 메모리에 있는 앱으로 직접 반환하는 URL 세션 작업.


* [Overview](#overview)
* [Relationships](#relationships)
* [See Also](#see-also)


&nbsp;    
## Overview
URLSessionDataTask는 [URLSessionTask](https://developer.apple.com/documentation/foundation/urlsessiontask)의 구상 서브 클래스이다. URLSessionDataTask 클래스의 메서드는 [URLSessionTask](https://developer.apple.com/documentation/foundation/urlsessiontask)에 설명되어있다.


데이터 작업은 하나 이상의 NSData 객체로 메모리에 있는 애플리케이션에 직접 데이터를 반환한다. 데이터 작업을 사용할 때:
* 본문(body) 데이터 업로드시(앱에서 제공하는 경우) 세션은 주기적으로 델리게이트의 [urlSession(_:task:didSendBodyData:totalBytesSent:totalBytesExpectedToSend:)](https://developer.apple.com/documentation/foundation/urlsessiontaskdelegate/1408299-urlsession) 메서드를 상태 정보와 함께 호출한다.
* 초기 응답을 받은 후 세션은 델리게이트의 [urlSession(_:dataTask:didReceive:completionHandler:)](https://developer.apple.com/documentation/foundation/urlsessiondatadelegate/1410027-urlsession) 메서드를 호출하여 상태 코드와 헤더를 검사하고 선택적으로 데이터 작업을 다운로드 작업으로 변환 할 수 있도록한다.
* 완료되면 세션은 해당 델리게이트의 [urlSession(_:dataTask:willCacheResponse:completionHandler:)](https://developer.apple.com/documentation/foundation/urlsessiondatadelegate/1411612-urlsession) 메서드를 호출하여 응답을 캐시해야하는지 여부를 결정할 수 있도록한다.


데이터 가져 오기 및 업로드에 대한 데이터 작업 사용에 대한 예는 [Fetching Website Data into Memory](https://developer.apple.com/documentation/foundation/url_loading_system/fetching_website_data_into_memory) 및 [Uploading Data to a Website](https://developer.apple.com/documentation/foundation/url_loading_system/uploading_data_to_a_website)를 참조.


&nbsp;
## Relationships
### Inherits From
* URLSessionTask


### Conforms To
* CVarArg
* Equatable
* Hashable


&nbsp;
## See Also
### Adding Data Tasks to a Session
* func dataTask(with: URL) -> URLSessionDataTask
* func dataTask(with: URL, completionHandler: (Data?, URLResponse?, Error?) -> Void) -> URLSessionDataTask
* func dataTask(with: URLRequest) -> URLSessionDataTask
* func dataTask(with: URLRequest, completionHandler: (Data?, URLResponse?, Error?) -> Void) -> URLSessionDataTask
* protocol URLSessionDataDelegate


&nbsp;      
&nbsp;      
### [by. 0junChoi](https://github.com/0jun0815) email: <0jun0815@gmail.com>
### [Apple Developer Documentation URLSessionDataTask](https://developer.apple.com/documentation/foundation/urlsessiondatatask)
