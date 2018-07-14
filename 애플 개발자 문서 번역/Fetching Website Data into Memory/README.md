# Fetching Website Data into Memory
> URL 세션에서 데이터 작업(task)을 생성하여 데이터를 메모리로 직접 수신한다.


* [Overview](#overview)
* [Receive Results with a Completion Handler](#receive-results-with-a-completion-handler)
* [Receive Transfer Details and Results with a Delegate](#receive-transfer-details-and-results-with-a-delegate)
* [See Also](#see-also)


&nbsp;    
## Overview
원격 서버와의 작은 상호 작용을 위해 [URLSessionDataTask](https://developer.apple.com/documentation/foundation/urlsessiondatatask) 클래스를 사용하여 응답 데이터를 메모리로 수신할 수 있다. ([URLSessionDownloadTask](https://developer.apple.com/documentation/foundation/urlsessiondownloadtask) 클래스를 사용하면 파일 시스템에 직접 데이터를 저장함). 데이터 작업은 웹 서비스 끝점(endpoint) 호출과 같은 작업에 이상적이다.


URL 세션 인스턴스를 사용하여 작업을 만든다. 요구가 비교적 간단하다면 [URLSession](https://developer.apple.com/documentation/foundation/urlsession) 클래스의 shared 인스턴스를 사용할 수 있다. 델리게이트 콜백을 통해 전송과 상호 작용하려는 경우 [shared](https://developer.apple.com/documentation/foundation/urlsession/1409000-shared) 인스턴스를 사용하는 대신 세션을 만들어야한다. 세션을 만들 때 [URLSessionConfiguration](https://developer.apple.com/documentation/foundation/urlsessionconfiguration) 인스턴스를 사용하고 [URLSessionDelegate](https://developer.apple.com/documentation/foundation/urlsessiondelegate) 또는 해당 서브 프로토콜 중 하나를 구현하는 클래스를 전달한다. 세션은 여러 작업을 생성하기 위해 재사용할 수 있으므로 필요로하는 고유한 구성마다 세션을 만들어 프로퍼티로 저장한다.


&nbsp;
> **Note**
>
> 필요 이상으로 세션을 생성하지 않도록 주의한다. 예를 들어 유사하게 구성된 세션이 필요한 앱의 여러 부분이 있는 경우 하나의 세션을 만들어 세션을 공유한다.


&nbsp;

세션이 있으면 dataTask() 메서드 중 하나를 사용하여 데이터 작업을 만든다. 작업은 일시 중단된 상태로 만들어지며 [resume()](https://developer.apple.com/documentation/foundation/urlsessiontask/1411121-resume)을 호출하여 시작할 수 있다.


&nbsp;      
## Receive Results with a Completion Handler
데이터를 가져오는 가장 간단한 방법은 완료 핸들러를 사용하는 데이터 작업을 만드는 것이다. 이 배열을 사용하면 작업은 사용자가 제공한 완료 핸들러 블록에 서버의 응답, 데이터 및 오류를 전달한다. 아래 그림은 세션과 작업간의 관계와 결과가 완료 핸들러에 전달되는 방식을 보여준다.


![](https://docs-assets.developer.apple.com/published/c7124fb5d7/bf4501ff-82b2-4dd4-9ec3-243ef0e70d21.png)


완료 핸들러를 사용하는 데이터 작업을 만들려면 URLSession의 [dataTask(with:)]() 메서드를 호출한다. 완료 핸들러는 다음 세 가지 작업을 수행해야 한다:
1. error 파라미터가 nil인지 확인한다. 그렇지 않은 경우 전송 오류가 발생했으므로 오류를 처리하고 종료한다.
2. response 파라미터를 확인하여 상태 코드가 성공을 나타내고 MIME 타입이 예상 값인지 확인한다. 그렇지 않으면 서버 오류를 처리하고 종료한다.
3. 필요에 따라 data 인스턴스를 사용한다.


Listing 1은 URL 콘텐츠를 가져오기 위한 startLoad() 메서드를 보여준다. URLSession 클래스의 shared 인스턴스를 사용하여 결과를 완료 핸들러에 전달하는 데이터 작업을 만든다. 로컬 및 서버 오류를 확인한 후 이 핸들러는 데이터를 문자열로 변환하고 이를 사용하여 WKWebView 아울렛을 채운다. 물론, 데이터 모델로 파싱하는 것과 같이 가져온 데이터를 다른 용도로 사용할 수도 있다.


**Listing 1** Creating a completion handler to receive data-loading results
```
func startLoad() {
    let url = URL(string: "https://www.example.com/")!
    let task = URLSession.shared.dataTask(with: url) { data, response, error in
        if let error = error {
            self.handleClientError(error)
            return
        }
        guard let httpResponse = response as? HTTPURLResponse,
            (200...299).contains(httpResponse.statusCode) else {
            self.handleServerError(response)
            return
        }
        if let mimeType = httpResponse.mimeType, mimeType == "text/html",
            let data = data,
            let string = String(data: data, encoding: .utf8) {
            DispatchQueue.main.async {
                self.webView.loadHTMLString(string, baseURL: url)
            }
        }
    }
    task.resume()
}
```


&nbsp;
> **Important**
>
> 완료 핸들러는 작업을 생성 한 것과는 다른 Grand Central Dispatch 큐에서 호출된다. 따라서 데이터를 표시하거나 오류를 사용하여 UI를 업데이트하는 모든 작업(예: WebView 업데이트)은 여기에 표시된 것처럼 명시적으로 매인 큐에 배치해야한다. 


&nbsp;
## Receive Transfer Details and Results with a Delegate
데이터 작업을 생성 할 때 진행되는 작업의 액티비티에 대한 더 높은 수준의 접근을 위해 완료 핸들러를 제공하는 대신 세션에서 델리게이트를 설정할 수 있다. 아래 그림은 이 배열을 보여준다.


![](https://docs-assets.developer.apple.com/published/8b22355c7f/730c8e1b-654f-4eb9-9c63-d439a69ac5d2.png)


이 방법을 사용하면 전송이 완료되거나 오류가 발생하여 실패 할 때까지 데이터의 일부가 [URLSessionDataDelegate](https://developer.apple.com/documentation/foundation/urlsessiondatadelegate)의 [urlSession(_:dataTask:didRecive:)](https://developer.apple.com/documentation/foundation/urlsessiondatadelegate/1411528-urlsession) 메서드에 전달된다. 델리게이트는 전송이 진행됨에 따라 다른 종류의 이벤트도 받는다.


델리게이트 방식을 사용하는 경우 URLSession 클래스의 단순 shared 인스턴스를 사용하는 대신 고유한 URLSession 인스턴스를 만들어야 한다. 새로운 세션을 생성하면 자신의 클래스를 세션의 델리게이트로 설정할 수 있다(Listing 2 참조).


클래스가 하나 이상의 델리게이트 프로토콜 ([URLSessionDelegate](https://developer.apple.com/documentation/foundation/urlsessiondelegate), [URLSessionTaskDelegate](https://developer.apple.com/documentation/foundation/urlsessiontaskdelegate), [URLSessionDataDelegate](https://developer.apple.com/documentation/foundation/urlsessiondatadelegate), [URLSessionDownloadDelegate](https://developer.apple.com/documentation/foundation/urlsessiondownloaddelegate))을 구현한다고 선언한다. 그런 다음 이니셜 라이저 [init(configuration:delegate:delegateQueue:)](https://developer.apple.com/documentation/foundation/urlsession/1411597-init)를 사용하여 URL 세션 인스턴스를 만든다. 이 초기화와 함께 사용되는 구성 인스턴스를 커스텀화 할 수 있다. 예를 들어 [waitForConnectivity](https://developer.apple.com/documentation/foundation/urlsessionconfiguration/2908812-waitsforconnectivity)를 true로 설정하는 것이 좋다. 그러면 세션은 필요한 연결을 사용할 수 없는 경우 즉시 실패하지 않고 적절한 연결을 기다린다.


**Listing 2** Creating a URLSession that uses a delegate
```
private lazy var session: URLSession = {
    let configuration = URLSessionConfiguration.default
    configuration.waitsForConnectivity = true
    return URLSession(configuration: configuration,
                    delegate: self, delegateQueue: nil)
}()
```


Listing 3은 이 세션을 사용하여 데이터 작업을 시작하고 델리게이트 콜백을 사용하여 수신된 데이터 및 오류를 처리하는 startLoad() 메서드를 보여준다. 이 코드는 세 가지 델리게이트 콜백을 구현한다:
* [urlSession(_:dataTask:didReceive:completionHandler:)](https://developer.apple.com/documentation/foundation/urlsessiondatadelegate/1410027-urlsession) 응답에 성공한 HTTP 상태 코드가 있고 MIME 타입이 text/html 또는 text/plain인지 확인한다. 둘 중 하나라도 해당하지 않으면 작업이 취소된다. 그렇지 않으면 계속 진행할 수 있다.
* [urlSession(_:dataTask:didReceive:)](https://developer.apple.com/documentation/foundation/urlsessiondatadelegate/1411528-urlsession) 작업에서 받은 각 Data 인스턴스를 받아서 receivedData라는 버퍼에 추가한다.
* [urlSession(_:task:didCompleteWithError:)](https://developer.apple.com/documentation/foundation/urlsessiontaskdelegate/1411610-urlsession) 먼저 전송 수준 오류가 발생했는지 확인한다. 오류가 없으면 receivedData 버퍼를 문자열로 변환하여 webView의 콘텐츠로 설정하려고 시도한다.


**Listing 3** Using a delegate with a URL session data task
```
var receivedData: Data?

func startLoad() {
    loadButton.isEnabled = false
    let url = URL(string: "https://www.example.com/")!
    receivedData = Data()
    let task = session.dataTask(with: url)
    task.resume()
}

// delegate methods

func urlSession(_ session: URLSession, dataTask: URLSessionDataTask, didReceive response: URLResponse,
                completionHandler: @escaping (URLSession.ResponseDisposition) -> Void) {
    guard let response = response as? HTTPURLResponse,
        (200...299).contains(response.statusCode),
        let mimeType = response.mimeType,
        mimeType == "text/html" else {
        completionHandler(.cancel)
        return
    }
    completionHandler(.allow)
}

func urlSession(_ session: URLSession, dataTask: URLSessionDataTask, didReceive data: Data) {
    self.receivedData?.append(data)
}

func urlSession(_ session: URLSession, task: URLSessionTask, didCompleteWithError error: Error?) {
    DispatchQueue.main.async {
        self.loadButton.isEnabled = true
        if let error = error {
            handleClientError(error)
        } else if let receivedData = self.receivedData,
            let string = String(data: receivedData, encoding: .utf8) {
            self.webView.loadHTMLString(string, baseURL: task.currentRequest?.url)
        }
    }
}
```


다양한 델리게이트 프로토콜은 위의 코드에 표시된 것 이외의 메서드로 인증 문제를 처리하고 리다이렉션 및 기타 특수한 경우를 처리한다. URLSession 토론의 [Using a URLSession](https://developer.apple.com/documentation/foundation/urlsession#2926844)에서 전송 중 발생할 수 있는 다양한 콜백을 설명한다. 


&nbsp;
## See Also
### First Step
* [Uploading Data to Website](https://developer.apple.com/documentation/foundation/url_loading_system/uploading_data_to_a_website)
* [Downloading Files in the Background](https://developer.apple.com/documentation/foundation/url_loading_system/downloading_files_in_the_background)
* class URLSession
* class URLSessionConfiguration
* class URLSessionTask


&nbsp;      
&nbsp;      
### [by. 0junChoi](https://github.com/0jun0815) email: <0jun0815@gmail.com>
### [Apple Developer Documentation Fetching Website Data into Memory](https://developer.apple.com/documentation/foundation/url_loading_system/fetching_website_data_into_memory)
