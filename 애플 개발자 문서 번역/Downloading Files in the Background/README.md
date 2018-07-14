# Downloading Files in the Background
> 앱이 비활성 상태인 동안 파일을 다운로드하는 작업을 만든다.


* [Overview](#overview)
* [Configure the Background Session](#configure-the-background-session)
* [Create and Schedule the Download Task](#create-and-schedule-the-download-task)
* [Handle App Suspension](#handle-app-suspension)
* [Recreate the Session If the App Was Terminated](#recreate-the-session-if-the-app-was-terminated)
* [Handle Download Completion and Errors](#handle-download-completion-and-errors)
* [Comply with Background Transfer Limitations](#comply-with-background-transfer-limitations)
* [See Also](#see-also)


&nbsp;    
## Overview
장기 실행 및 긴급 전송의 경우 백그라운드에서 실행되는 작업을 생성할 수 있다. 이 작업은 앱이 일시 중지 된 경우에도 계속 실행되므로 앱 재개시 앱이 다운로드 한 파일에 액세스 할 수 있다.


> **Note**
>
> 여기서 설명한대로 백그라운드 네트워크 작업을 모드 백그라운드 네트워크로 수행해야 하는 것은 아니다. 적절한 백그라운드 모드를 선언하는 앱은 마치 포그라운드에 있는 것처럼 기본 URL 세션 및 데이터 작업을 사용할 수 있다.


&nbsp;
## Configure the Background Session
다음과 같이 백그라운드 URL 세션을 만든다. Listing 1에서는 이 프로세스를 보여준다.
1. [URLSession](https://developer.apple.com/documentation/foundation/urlsession)의 클래스 메서드인 [background(withIdentifier:)](https://developer.apple.com/documentation/foundation/urlsessionconfiguration/1407496-background)를 사용하여 백그라운드 [URLSessionConfiguration](https://developer.apple.com/documentation/foundation/urlsessionconfiguration) 객체를 만들고 앱에서 고유한 세션 식별자를 제공한다. 대부분의 앱은 백그라운드 세션이 거의 필요하지 않으므로(일반적으로 하나의 세션) 동적으로 생성된 식별자가 아닌 고정된 문자열을 식별자에 사용할 수 있다. 식별자는 전역적으로 유일하지 않아도 된다.
2. 작업이 완료되고 애플리케이션이 백그라운드에서 실행될 때 시스템이 애플리케이션을 깨우게하려면 [sessionSendsLaunchEvents](https://developer.apple.com/documentation/foundation/urlsessionconfiguration/1617174-sessionsendslaunchevents) 프로퍼티가 true(기본값)로 설정되어 있는지 확인한다.
3. 시간에 민감하지 않은 작업의 경우 [isDiscretionary]() 프로퍼티를 사용하도록 설정하여 장치가 충전중이거나 Wi-Fi에 연결될 때와 같이 전송을 수행하기위한 최적의 조건을 기다릴 수 있다.
4. URLSession 인스턴스를 작성하려면, URLSessionConfiguration 인스턴스를 사용한다. 세션은 백그라운드 전송에서 이벤트를 수신하는 델리게이트를 제공해야 한다.


**Listing 1** Creating a background URL session
```
private lazy var urlSession: URLSession = {
    let config = URLSessionConfiguration.background(withIdentifier: "MySession")
    config.isDiscretionary = true
    config.sessionSendsLaunchEvents = true
    return URLSession(configuration: config, delegate: self, delegateQueue: nil)
}()
```


> **Note**
>
> 시스템의 스케줄링 및 백그라운드 작업 수행에 대한 가시성을 높이기 위해, Bug Reporting [Profiles and Logs](https://developer.apple.com/bug-reporting/profiles-and-logs/) 페이지에서 iOS 장치에서 백그라운드 네트워킹 프로파일을 다운로드하여 설치한다.


&nbsp;
## Create and Schedule the Download Task
URL을 사용하는 [downloadTask(with:)](https://developer.apple.com/documentation/foundation/urlsession/1411482-downloadtask) 메서드 또는 [URLRequst](https://developer.apple.com/documentation/foundation/urlrequest) 인스턴스를 사용하는 [downloadTask(with:)](https://developer.apple.com/documentation/foundation/urlsession/1411481-downloadtask) 메서드를 사용하여 세션에서 다운로드 작업을 생성한다. 시스템이 동작을 최적화하는 데 도움이 되도록 이 메서드의 프로퍼티를 설정한다.
1. Listing 2에서와 같이 [downloadTask(with:)](https://developer.apple.com/documentation/foundation/urlsession/1411482-downloadtask)를 사용하여 다운로드 작업을 생성한다.
2. 필요에 따라 [earliestBeginDate](https://developer.apple.com/documentation/foundation/urlsessiontask/2873413-earliestbegindate) 프로퍼티를 설정하여 향후 다운로드가 시작하도록 스케줄링한다. 다운로드가 이번에 정확하게 시작되는 것은 아니지만, 더 빨리 시작되지도 않는다. 
3. 시스템이 네트워크 활동을 효율적으로 스케줄 할 수 있게 하려면 [countOfBytesClientExpectsToSend](https://developer.apple.com/documentation/foundation/urlsessiontask/2873401-countofbytesclientexpectstosend) 및 [countOfBytesClientExpectsToReceive](https://developer.apple.com/documentation/foundation/urlsessiontask/2873414-countofbytesclientexpectstorecei) 프로퍼티를 설정한다. 이 값은 예상되는 바이트 수에 대한 최상의 상한값이며 헤더와 바디(본문) 데이터를 고려해야한다.
4. [resume()](https://developer.apple.com/documentation/foundation/urlsessiontask/1411121-resume)을 호출하여 작업을 시작한다.


**Listing 2** Creating a download task from a URL session
```
let backgroundTask = urlSession.downloadTask(with: url)
backgroundTask.earliestBeginDate = Date().addingTimeInterval(60 * 60)
backgroundTask.countOfBytesClientExpectsToSend = 200
backgroundTask.countOfBytesClientExpectsToReceive = 500 * 1024
backgroundTask.resume()
```


&nbsp;
## Handle App Suspension
다른 앱 상태는 앱이 백그라운드 다운로드와 상호 작용하는 방식에 영향을준다. iOS에서는 앱이 포그라운드에 있거나 일시 중지되었거나 시스템에 의해 종료 될 수 있다. 이러한 상태에 대한 자세한 내용은 [Managing Your App's Life Cycle](https://developer.apple.com/documentation/uikit/core_app/managing_your_app_s_life_cycle)을 참조한다.


앱이 백그라운드에 있는 경우 다운로드가 다른 프로세스에서 수행되는 동안 앱이 일시 중지될 수 있다. 이 경우 다운로드가 끝나면 시스템에서 애플리케이션을 다시 시작하고 [UIApplicationDelegate](https://developer.apple.com/documentation/uikit/uiapplicationdelegate)의 메서드 [application(_:handleEventsForBackgroundURLSession:completionHandler:)](https://developer.apple.com/documentation/uikit/uiapplicationdelegate/1622941-application)를 호출한다. 이 메서드는 Listing 1에서 작성한 세션 식별자를 두 번째 매개변수로 받는다.


이 델리게이트 메서드는 또한 최종 매개변수로 완료 핸들러를 받는다. 이 핸들러는 앱용으로 이해할 수 있는 곳이면 어디든지 앱 델리게이트의 프로퍼티나 URLSessionDownloadDelegate를 구현하는 클래스의 프로퍼티로 저장해야 한다. Listing 3에서 이 완료 핸들러는 backgroundCompletionHandler라는 애플리케이션 델리게이트 프로퍼티에 저장된다.


**Listing 3** Storing the background download completion handler sent to the application delegate
```
func application(_ application: UIApplication,
                handleEventsForBackgroundURLSession identifier: String,
                completionHandler: @escaping () -> Void) {
    backgroundCompletionHandler = completionHandler
}
```


모든 이벤트가 전달되면 시스템은 [URLSessionDelegate](https://developer.apple.com/documentation/foundation/urlsessiondelegate)의 [urlSessionDidFinishEvents(forBackgroundURLSession:)](https://developer.apple.com/documentation/foundation/urlsessiondelegate/1617185-urlsessiondidfinishevents) 메서드를 호출한다. 이 시점에서, 앱 델리게이트에 의해 저장된 backgroundCompletionHandler를 가져와서 그것을 실행한다. Listing 4는 이 프로세스를 보여준다.


urlSessionDidFinishEvents(forBackgroundURLSession:)은 보조 큐에서 호출 될 수 있으므로 메인 큐에서 처리 실행(UIKit 메서드에서 수신)을 명시적으로 실행해야 한다.


**Listing 4** Executing the background URL session completion handler on the main queue
```
func urlSessionDidFinishEvents(forBackgroundURLSession session: URLSession) {
    DispatchQueue.main.async {
        guard let appDelegate = UIApplication.shared.delegate as? AppDelegate,
            let backgroundCompletionHandler =
            appDelegate.backgroundCompletionHandler else {
            return
        }
        backgroundCompletionHandler()
    }
}
```


&nbsp;
## Recreate the Session If the App Was Terminated
앱이 일시 중지 된 상태에서 앱을 종료하면 백그라운드에서 앱이 다시 시작된다. 시작 시간 설정의 일부로 이전과 동일한 세션 식별자를 사용하여 백그라운드 세션을 다시 생성하여(Listing 1 참조) 시스템이 백그라운드 다운로드 작업을 세션과 다시 연결할 수 있게 한다. 사용자 또는 시스템에서 애플리케이션을 시작했는지 여부에 관계없이 백그라운드 세션을 시작할 준비가 된다. 앱이 다시 시작되면 [Handle App Suspension](https://developer.apple.com/documentation/foundation/url_loading_system/downloading_files_in_the_background#2928518)에서 설명한 것처럼 앱이 일시 중지되고 다시 시작된 것과 같은 일련의 이벤트가 발생한다.


> **Note**
>
> 앱이 백그라운드에 있는 동안 전송이 시작된 경우 세션 구성의 isDiscretionary 프로퍼티가 true로 처리된다.


&nbsp;
## Handle Download Completion and Errors
앱이 다시 시작된 후(또는 포그라운드에 있는 경우) [URLSessionDownloadDelegate](https://developer.apple.com/documentation/foundation/urlsessiondownloaddelegate) 구현은 전송 상태를 업데이트하기 위해 콜백을 받는다.


[urlSession(_:downloadTask:didFinishDownloadingTo:)](https://developer.apple.com/documentation/foundation/urlsessiondownloaddelegate/1411575-urlsession)를 구현하여 다운로드 완료를 처리한다. 404와 같은 서버측 오류 코드가 표시되는지 확인하려면 downloadTask response를 확인한다. 그렇다면 다운로드 할 파일이 없으므로 중단해야 한다. 다운로드가 성공하면 최종 매개변수는 파일이 저장된 로컬 URL이다. 이 위치는 콜백이 끝날 때까지만 유효하므로 앱의 Documents 디렉토리와 같은 다른 위치로 이동해야 한다.


Listing 5는 [urlSession(_:downloadTask:didFinishDownloadingTo:)](https://developer.apple.com/documentation/foundation/urlsessiondownloaddelegate/1411575-urlsession)의 구현을 보여준다. 구현은 서버 측 오류를 확인한 다음 파일을 Documents 디렉토리로 이동하여 큰 파일의 백그라운드 다운로드를 완료한다.


**Listing 5** Retrieving the downloaded file
```
func urlSession(_ session: URLSession, downloadTask: URLSessionDownloadTask,
                didFinishDownloadingTo location: URL) {
    guard let httpResponse = downloadTask.response as? HTTPURLResponse,
        (200...299).contains(httpResponse.statusCode) else {
        print ("server error")
        return
    }
    do {
        let documentsURL = try
            FileManager.default.url(for: .documentDirectory,
                                    in: .userDomainMask,
                                    appropriateFor: nil,
                                    create: false)
        let savedURL = documentsURL.appendingPathComponent(
            location.lastPathComponent)
        try FileManager.default.moveItem(at: location, to: savedURL)
    } catch {
        print ("file error: \(error)")
    }
}
```


호스트에 연결하지 못하는 것처럼 클라이언트쪽 다운로드 오류를 처리하려면 [urlSession(_:task:didCompleteWithError:)](https://developer.apple.com/documentation/foundation/urlsessiontaskdelegate/1411610-urlsession)를 구현한다. 이 메서드는 작업이 완료 될 때 호출된다. 마지막 매개변수가 nil이 아닌 경우 오류만 처리하면 된다.


&nbsp;
## Comply with Background Transfer Limitations
백그라운드 세션에서 실제 전송은 앱의 프로세스와 별도의 프로세스에 의해 수행된다. 앱의 프로세스를 재시작하는 것이 상당히 비싸기 때문에 몇 가지 기능을 사용할 수 없으므로 다음과 같은 제한이 있다:
* 세션은 이벤트 전달을 위한 델리게이트를 제공해야 한다. 업로드 및 다운로드의 경우 델리게이트는 in-process 전송의 경우와 동일하게 작동한다.
* HTTP 및 HTTPS 프로토콜만 지원된다(커스텀 프로토콜은 없음).
* 리다이렉션은 항상 준수된다. 따라서 [urlSession(_:task:willPerformHTTPRedirection:newRequest:completionHandler:)](https://developer.apple.com/documentation/foundation/urlsessiontaskdelegate/1411626-urlsession)를 구현한 경우에도 호출되지 않는다.
* 파일에서 업로드 작업만 지원된다(데이터 인스턴스의 업로드 또는 스트림이 앱 종료 후 실패한다).


&nbsp;
## See Also
### First Step
* [Fetching Website Data into Memory](https://developer.apple.com/documentation/foundation/url_loading_system/fetching_website_data_into_memory)
* [Uploading Data to a Website](https://developer.apple.com/documentation/foundation/url_loading_system/uploading_data_to_a_website)
* class URLSession
* class URLSessionConfiguration
* class URLSessionTask


&nbsp;      
&nbsp;      
### [by. 0junChoi](https://github.com/0jun0815) email: <0jun0815@gmail.com>
### [Apple Developer Documentation Downloading Files in the Background](https://developer.apple.com/documentation/foundation/url_loading_system/downloading_files_in_the_background)
