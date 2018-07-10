# UIAlertAction
> 사용자가 얼럿의 버튼을 누를 때 수행 할 수 있는 액션이다.


* [Overview](#overview)
* [Topics](#topics)
    * [Creating an Alert Action](#creating-an-alert-action)
    * [Getting the Action’s Attributes](#getting-the-actions-attributes)
    * [Constants](#constants)
* [Relationships](#relationships)
* [See Also](#see-also)


&nbsp;    
## Overview
이 클래스를 사용하여 버튼에 표시할 제목, 스타일 정보 및 사용자가 버튼을 누를 때 실행할 핸들러를 포함하여 단일 액션에 대한 정보를 구성할 수 있다. 얼럿 액션 객체를 생성한 후 해당 얼럿을 사용자에게 표시하기 전에 [UIAlertController](https://developer.apple.com/documentation/uikit/uialertcontroller) 객체에 추가한다.


&nbsp;      
## Topics
### Creating an Alert Action
> 얼럿 액션 생성

* `init(title: String?, style: UIAlertAction.Style, handler: ((UIAlertAction) -> Void)? = nil)`
    * 지정된 제목 및 동작으로 액션을 만들고 반환한다.
    

### Getting the Action’s Attributes
> 액션의 속성 가져오기

* `var title: String?`
    * 액션의 버튼의 타이틀.
* `var style: UIAlertAction.Style`
    * 액션의 버튼에 적용되는 스타일이다.
* `var isEnabled: Bool`
    * 액션이 현재 사용 가능한지 여부를 나타내는 부울 값이다.
    

### Constants
* `enum UIAlertAction.Style`
    * 얼럿의 액션 버튼에 적용할 스타일이다.


& nbsp;
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
### Alerts
* [Getting the User's Attention with Alerts and Action Sheets](https://developer.apple.com/documentation/uikit/windows_and_screens/getting_the_user_s_attention_with_alerts_and_action_sheets)
* class UIAlertController


&nbsp;      
&nbsp;      
### [by. 0junChoi](https://github.com/0jun0815) email: <0jun0815@gmail.com>
### [Apple Developer Documentation UIAlertAction](https://developer.apple.com/documentation/uikit/uialertaction)
