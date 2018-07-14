# Uploading Data to a Website
> 앱의 데이터를 서버에 게시한다.


* [Overview](#overview)
* [Prepare Your Data for Upload](#prepare-your-data-for-upload)
* [Configure an Upload Request](#configure-an-upload-request)
* [Create and Start an Upload Task](#create-and-start-an-upload-task)
* [Alternatively, Upload by Setting a Delegate](#alternatively-upload-by-setting-a-delegate)
* [See Also](#see-also)


&nbsp;    
## Overview
많은 애플케이션은 이미지나 문서와 같은 파일의 업로드를 허용하거나 JSON과 같은 구조화 된 데이터를 허용하는 웹 서비스 API 앤드포인트를 사용하는 서버에 작동한다. 앱에서 데이터를 업로드하려면 [URLSession](https://developer.apple.com/documentation/foundation/urlsession) 인스턴스를 사용하여 [URLSessionUploadTask](https://developer.apple.com/documentation/foundation/urlsessionuploadtask) 인스턴스를 만든다. 업로드 작업은 업로드 수행 방법을 자세히 설명하는 [URLRequest](https://developer.apple.com/documentation/foundation/urlrequest) 인스턴스를 사용한다.


&nbsp;
## Prepare Your Data for Upload
업로드 할 데이터는 Listing1의 경우처럼 파일, 스트림 또는 데이터의 콘텐츠가 될 수 있다.


많은 웹 서비스 앤드포인트에서는 배열 및 딕셔너리와 같은 인코딩 가능 타입에서 JSONEncoder 클래스를 사용하여 JSON 형식의 데이터를 만든다. Listing 1에서 보듯이, Codable을 준수하는 구조체를 선언하고, 이 타입의 인스턴스를 만든 다음 JSONEncoder를 사용하여 인스턴스를 업도드 용 JSON 데이터로 인코딩 할 수 있다.


**Listing 1** Preparing JSON data for upload
```
struct Order: Codable {
    let customerId: String
    let items: [String]
}

// ...

let order = Order(customerId: "12345",
                    items: ["Cheese pizza", "Diet soda"])
guard let uploadData = try? JSONEncoder().encode(order) else {
    return
}
```


이미지를 JPEG 또는 PNG 데이터로 인코딩하거나 UTF-8과 같은 인코딩을 사용하여 문자열을 데이터로 변환하는 등의 다른 여러가지 방법으로 데이터 인스턴스를 만들 수 있다.


&nbsp;
## Configure an Upload Request
업로드 작업에는 URLRequest 인스턴스가 필요하다. Listing 2에서 보듯이, 서버가 지원하고 기대하는 것에 따라 request의 [httpMethod](https://developer.apple.com/documentation/foundation/urlrequest/2011415-httpmethod) 프로퍼티를 "POST" 또는 "PUT"으로 설정한다. [setValue(_:forHTTPHeaderField:)](https://developer.apple.com/documentation/foundation/urlrequest/2011447-setvalue) 메서드를 사용하여 Content-Length 헤더를 제외하고 제공 할 HTTP 헤더의 값을 설정한다. 세션은 데이터 크기에 따라 콘텐츠 길이를 자동으로 계산한다.


**Listing 2** Configuring a URL request
```
let url = URL(string: "https://example.com/post")!
var request = URLRequest(url: url)
request.httpMethod = "POST"
request.setValue("application/json", forHTTPHeaderField: "Content-Type")
```


&nbsp;
## Create and Start an Upload Task
업로드를 시작하려면 URLSession 인스턴스에서 [uploadTask(with:completionHandler:)](https://developer.apple.com/documentation/foundation/urlsession/1411518-uploadtask)를 호출하여 업로드 [URLSessionTask](https://developer.apple.com/documentation/foundation/urlsessiontask) 인스턴스를 만들고 이전에 설정 한 request와 data 인스턴스를 전달한다. 작업이 일시 중단 된 상태에서 시작하기 때문에 작업에서 [resume()](https://developer.apple.com/documentation/foundation/urlsessiontask/1411121-resume)을 호출하여 네트워크 로드 프로세스를 시작한다. Listing 3은 shared URLSession 인스턴스를 사용하고 완료 핸들러에서 결과를 수신한다. 핸들러는 리턴된 data를 사용하기전에 전송과 서버 에러를 확인한다.


**Listing 3** Starting an upload task
```
let task = URLSession.shared.uploadTask(with: request, from: uploadData) { data, response, error in
    if let error = error {
        print ("error: \(error)")
        return
    }
    guard let response = response as? HTTPURLResponse,
        (200...299).contains(response.statusCode) else {
        print ("server error")
        return
    }
    if let mimeType = response.mimeType,
        mimeType == "application/json",
        let data = data,
        let dataString = String(data: data, encoding: .utf8) {
        print ("got data: \(dataString)")
    }
}
task.resume()
```


&nbsp;
## Alternatively, Upload by Setting a Delegate
완료 핸들러 접근 방식 대신, 구성한 세션에 델리게이트를 설정 한 다음 [uploadTask(with:from:)](https://developer.apple.com/documentation/foundation/urlsession/1409763-uploadtask)을 사용하여 업로드 작업을 만들 수 있다. 이 시나리오에서는 [URLSessionDelegate](https://developer.apple.com/documentation/foundation/urlsessiondelegate) 및 [URLSessionTaskDelegate](https://developer.apple.com/documentation/foundation/urlsessiontaskdelegate) 프로토콜을 사용하여 메서드를 구현한다. 이 메서드는 서버 응답과 모든 데이터 또는 전송 오류를 수신한다.


&nbsp;
## See Also
### First Step
* [Fetching Website Data into Memory](https://developer.apple.com/documentation/foundation/url_loading_system/fetching_website_data_into_memory)
* [Downloading Files in the Background](https://developer.apple.com/documentation/foundation/url_loading_system/downloading_files_in_the_background)
* class URLSession
* class URLSessionConfiguration
* class URLSessionTask


&nbsp;      
&nbsp;      
### [by. 0junChoi](https://github.com/0jun0815) email: <0jun0815@gmail.com>
### [Apple Developer Documentation Uploading Data to a Website](https://developer.apple.com/documentation/foundation/url_loading_system/uploading_data_to_a_website)
