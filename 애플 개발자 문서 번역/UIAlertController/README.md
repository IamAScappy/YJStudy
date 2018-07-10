# UIAlertController
> 사용자에게 경고 메시지를 표시하는 객체이다.


* [Overview](#overview)
* [Topics](#topics)
* [Relationships](#relationships)
* [See Also](#see-also)


&nbsp;    
## Overview
이 클래스를 사용하여 표시할 메시지와 선택할 작업이 포함 된 얼럿 및 액션 시트를 구성한다. 원하는 액션과 스타일로 얼럿 컨트롤러를 구성한 후 [present(_:animated:completion:)](https://developer.apple.com/documentation/uikit/uiviewcontroller/1621380-present) 메서드를 사용하여 나타낸다. UIKit은 앱의 컨텐츠 위에 모달로 얼럿 및 액션 시트를 표시한다. 


사용자에게 메시지를 표시하는 것 외에도 작업을 얼럿 컨트롤러와 연결하여 사용자가 응답할 수 있는 방법을 제공할 수 있다. [addAction(_:)](https://developer.apple.com/documentation/uikit/uialertcontroller/1620094-addaction) 메서드를 사용하여 추가하는 각 액션에 대해 얼럿 컨트롤러는 액션 세부 정보가 있는 버튼은 구성한다. 사용자가 해당 액션을 누르면 얼럿 컨트롤러는 액션 객체를 만들 때 제공한 블록을 실행한다. 아래 코드는 단일 액션으로 얼럿을 구성하는 방법을 보여준다.


```
let alert = UIAlertController(title: "My Alert", message: "This is an alert.", preferredStyle: .alert) 
alert.addAction(UIAlertAction(title: NSLocalizedString("OK", comment: "Default action"), style: .default, handler: { _ in 
NSLog("The \"OK\" alert occured.")
}))
self.present(alert, animated: true, completion: nil)
```


[UIAlertController.Style.alert](https://developer.apple.com/documentation/uikit/uialertcontroller/style/alert) 스타일로 얼럿을 구성할 때 얼렇 인터페이스에 텍스트 필드를 추가할 수도 있다. 얼럿 컨트롤러를 사용하면 표시하기 전에 텍스트 필드 구성을 위한 블록을 제공할 수 있다. 얼럿 컨트롤러는 나중에 해당 값에 액세스 할 수 있도록 각 텍스트 필드에 대한 참조를 유지 관리한다.


&nbsp;
> **Important**
>
> UIAlertController 클래스는 그대로 사용하도록 되어 있으며 서브 클래싱을 지원하지 않는다. 이 클래스의 뷰 계층 구조는 비공개이며 수정할 수 없다.


&nbsp;      
## Topics
### Creating an Alert Controller
> 얼럿 컨트롤러 만들기

* `init(title: String?, message: String?, preferredStyle: UIAlertController.Style)`
    * 사용자에게 얼럿을 표시하기 위한 뷰 컨트롤러를 만들어 반환한다.
    

### Configuring the Alert
> 얼럿 구성

* `var title: String?`
    * 얼럿의 타이틀.
* `var message: String?`
    * 얼럿의 원인에 대해 자세히 설명하는 설명 텍스트.
* `var preferredStyle: UIAlertController.Style`
    * 얼럿 컨트롤러의 스타일.
    

### Configuring the User Actions
> 유저 액션 구성

* `func addAction(UIAlertAction)`   
    * 얼럿 또는 액션 시트에 액션 객체를 첨부한다.
* `var actions: [UIAlertAction]`
    * 얼럿 또는 액션 시트에 대한 응답으로 사용자가 취할 수 있는 액션들.
* `var preferredAction: UIAlertAction?`
    * 사용자가 얼럿에서 취하는 기본 액션.
    

### Configuring Text Fields
> 텍스트 필드 구성

* `func addTextField(configurationHandler: ((UITextField) -> Void)? = nil)`
    * 얼럿에 텍스트 필드를 추가한다.
* `var textFields: [UITextField]?`
    * 얼럿이 표시하는 텍스트 필드 배열.
    

### Constants
* `enum UIAlertController.Style`
    * 표시하는 얼럿의 타입을 나타내는 상수.


&nbsp;      
## Relationships
### Inherits From
* UIViewController


### Conforms To
* CVarArg
* Equatable
* Hashable
* NSExtensionRequestHandling
* UIPasteConfigurationSupporting
* UISpringLoadedInteractionSupporting
* UIStateRestoring
* UIUserActivityRestoring


&nbsp;
## See Also
### Alerts
* [Getting the User's Attention with Alerts and Action Sheets](https://developer.apple.com/documentation/uikit/windows_and_screens/getting_the_user_s_attention_with_alerts_and_action_sheets)
* class AlertAction


&nbsp;      
&nbsp;      
### [by. 0junChoi](https://github.com/0jun0815) email: <0jun0815@gmail.com>
### [Apple Developer Documentation UIAlertController](https://developer.apple.com/documentation/uikit/uialertcontroller)
