# UITabBarController
> 라디오-스타일 선택 인터페이스를 관리하는 컨테이너 뷰 컨트롤러. 여기서 선택은 표시할 자식 뷰 컨트롤러를 결정한다.


* [Overview](#overview)
* [The Views of a Tab Bar Controller](#the-views-of-a-tab-bar-controller)
* [The More Navigation Controller](#the-more-navigation-controller)
* [State Preservation](#state-preservation)
* [Differences in tvOS](#differences-in-tvos)
* [Topics](#topics)
    * [Customizing the Tab Bar Behavior](#customizing-the-tab-bar-behavior)
    * [Accessing the Tab Bar Controller Properties](#accessing-the-tab-bar-controller-properties)
    * [Managing the View Controllers](#managing-the-view-controllers)
    * [Managing the Selected Tab](#managing-the-selected-tab)
* [Relationships](#relationships)
* [See Also](#see-also)


&nbsp;    
## Overview
탭 바 인터페이스는 창 아래쪽에 여러 모드를 선택하고 해당 모드에 대한 뷰를 표시하는 탭을 표시한다. 이 클래스는 일반적으로 있는 그대로 사용되지만 서브 클래싱될 수도 있다.


탭 바 컨트롤러의 인터페이스의 각 탭은 커스텀 뷰 컨트롤러와 연결된다. 사용자가 특정 탭을 선택하면 탭 바 컨트롤러는 해당 뷰 컨트롤러의 루트 뷰를 표시하고 이전 뷰를 대체한다. 탭이 이미 선택되어 있어도 사용자 탭은 이전에 선택한 탭에 관계없이 탭의 루트 뷰를 항상 표시한다. 탭 선택은 인터페이스의 컨텐츠를 대체하기 때문에 각 탭에서 관리되는 인터페이스의 타입 탭이 어떤 식으로든 유사 할 필요는 없다. 사실 탭 바 인터페이스는 다른 타입의 정보를 제공하거나 완전히 다른 스타일의 인터페이스를 사용하여 동일한 정보를 제공하는데 일반적으로 사용된다. 아래 그림은 Clock app에서 제공하는 탭 바 인터페이스를 보여주는데, 각 탭은 시간 기반 정보 타입을 나타낸다.


&nbsp;
![](https://docs-assets.developer.apple.com/published/6ffdd16259/a4c30adf-176b-4020-ae69-f228edb9e621.png)
&nbsp;


탭 바 컨트롤러의 탭 바 뷰에 직접 액세스해서는 안된다. 탭 바 컨트롤러의 탭을 구성하려면 각 탭의 루트 뷰를 제공하는 뷰 컨트롤러를 [viewControllers](https://developer.apple.com/documentation/uikit/uitabbarcontroller/1621185-viewcontrollers) 프로퍼티에 할당한다. 뷰 컨트롤러를 지정하는 순서에 따라 탭 바에 나타나는 순서가 결정된다. 이 프로퍼티를 설정할 때 [selectedViewController](https://developer.apple.com/documentation/uikit/uitabbarcontroller/1621172-selectedviewcontroller) 프로퍼티에 값을 지정하여 처음에 어떤 뷰 컨트롤러가 선택되었는지 나타내야 한다. ([selectedIndex](https://developer.apple.com/documentation/uikit/uitabbarcontroller/1621171-selectedindex) 프로퍼티를 사용하여 배열 인덱스별로 뷰 컨트롤러를 선택할 수도 있다.) 앱 윈도우에 탭 바 컨트롤러의 뷰 (상속된 [view](https://developer.apple.com/documentation/uikit/uiviewcontroller/1621460-view) 프로퍼티를 사용하여 가져옴)를 포함하면 탭 바 컨트롤러가 자동으로 해당 뷰 컨트롤러를 선택하고 콘텐츠를 표시하며, 탭 바 인터페이스에 맞게 필요에 따라 크기를 조정한다.


탭 바 아이템은 해당 뷰 컨트롤러를 통해 구성된다. 탭 바 아이템을 뷰 컨트롤러와 연결하려면 [UITabBarItem](https://developer.apple.com/documentation/uikit/uitabbaritem) 클래스의 새 인스턴스를 만들고 뷰 컨트롤러에 대해 적절히 구성한 다음 뷰 컨트롤러의 [tabBarItem](https://developer.apple.com/documentation/uikit/uiviewcontroller/1621175-tabbaritem) 프로퍼티에 할당한다. 뷰 컨트롤러에 커스텀 탭 바 아이템을 제공하지 않으면 뷰 컨트롤러는 이미지가 없는 기본 아이템과 뷰 컨트롤러의 [title](https://developer.apple.com/documentation/uikit/uiviewcontroller/1621364-title) 프로퍼티에서 가져온 텍스트를 만든다.


사용자가 탭 바 인터페이스와 상호 작용할 때 탭 바 컨트롤러 객체는 해당 상호 작용에 대한 알림을 델리게이트에게 보낸다. 델리게이트는 사용자가 지정하는 모든 객체 일 수 있지만 [UITabBarControllerDelegate](https://developer.apple.com/documentation/uikit/uitabbarcontrollerdelegate) 프로토콜을 준수해야 한다. 델리게이트를 사용하여 특정 탭 바 항목이 선택되지 않도록하고 탭을 선택할 때 추가 작업을 수행 할 수 있다. 델리게이트를 사용하여 내비게이션 컨트롤러에서 만든 추가 탭 바의 변경 내용을 모니터링 할 수도 있다. 자세한 내용은 [The More Navigation Controller](https://developer.apple.com/documentation/uikit/uitabbarcontroller#1653016)를 참조.


&nbsp;
## The Views of a Tab Bar Controller
[UIViewController](https://developer.apple.com/documentation/uikit/uiviewcontroller) 클래스에서 UITabBarController 클래스를 상속하므로 탭 바 컨트롤러는 [view](https://developer.apple.com/documentation/uikit/uiviewcontroller/1621460-view) 프로퍼티를 통해 액세스할 수 있는 자체 뷰를 갖는다. 탭 바 컨트롤러에 대한 뷰는 탭 바 뷰 및 커스텀 콘텐츠가 포함 된 뷰의 컨테이너 일뿐이다. 탭 바 뷰는 사용자에 대한 선택 컨트롤을 하나 이상의 탭 바 아이템으로 구성된다. 아래 그림은 전반적인 탭 바 인터페이스를 제공하기 위해 이러한 뷰가 조합된 방법을 보여준다. 탭 바 및 툴바 뷰의 아이템은 변경할 수 있지만 아이템을 관리하는 뷰는 변경되지 않는다. 커스텀 콘텐츠 뷰만 현재 선택된 탭의 뷰 컨트롤러를 반영하여 변경된다.


&nbsp;
![](https://docs-assets.developer.apple.com/published/a0fd9e66d5/1bc595c9-a817-4057-b8b9-ecaa4e8647de.png)
&nbsp;


내비게이션 컨트롤러 또는 커스텀 뷰 컨트롤러를 탭의 루트 뷰 컨트롤러로 사용할 수 있다. 루트 뷰 컨트롤러가 내비게이션 컨트롤러인 경우 탭 바 컨트롤러는 표시된 내비게이션 콘텐츠의 크기를 조정하여 탭 바와 겹치지 않도록한다. 따라서 탭 바 인터페이스에 표시하는 모든 뷰는 모든 조건에서 뷰의 크기를 적절하게 조정하도록 [autoresizingMask](https://developer.apple.com/documentation/uikit/uiview/1622559-autoresizingmask) 프로퍼티를 설정해야 한다.


&nbsp;
## The More Navigation Controller
탭 바는 커스텀 아이템을 표시하기 위한 공간이 제한되어 있다. 탭 바 컨트롤러에 6개 이상의 커스텀 뷰 컨트롤러를 추가하면 탭 바 컨트롤러는 처음 네 개의 아이템과 표준 More 아이템만 탭 바에 표시한다. More 아이템을 누르면 나머지 아이템을 선택할 수 있는 표준 인터페이스가 나타난다.


표준 More 아이템의 인터페이스에는 사용자가 탭 바를 재구성 할 수 있는 편집 버튼이 있다. 기본적으로 사용자는 탭 바의 모든 아이템을 다시 정렬할 수 있다. 사용자가 일부 아이템을 수정하지 못하도록하려는 경우 [customizableViewControllers](https://developer.apple.com/documentation/uikit/uitabbarcontroller/1621184-customizableviewcontrollers) 프로퍼티의 배열에서 적절한 뷰 컨트롤러를 제거할 수 있다.


&nbsp;
> **Note**
>
> tvOS에서는 탭 바 커스터마이징 및 기타 인터페이스를 사용할 수 없다.


&nbsp;
## State Preservation
iOS 6 이상에서 이 뷰 컨트롤러의 [restorationIdentifier](https://developer.apple.com/documentation/uikit/uiviewcontroller/1621499-restorationidentifier) 프로퍼티에 값을 할당하면 선택한 탭의 뷰 컨트롤러에 대한 참조가 유지된다. 복원시, 참조를 사용하여 동일한 뷰 컨트롤러가 있는 탭을 선택한다.


탭 바 컨트롤러를 유지할 때 보존하려는 자식 뷰 컨트롤러에 고유한 복원 식별자를 할당한다. 자식 뷰 컨트롤러에서 복원 식별자를 생략하면 해당 탭이 기본 구성으로 돌아간다. 탭 바 컨트롤러는 [viewControllers](https://developer.apple.com/documentation/uikit/uitabbarcontroller/1621185-viewcontrollers) 프로퍼티에 나열된 것과 동일한 순서로 탭을 저장하지만 저장 순서는 실제로는 관련이 없다. 코드는 다음 실행주기 동안 새 탭 바 컨트롤러를 제공해야하므로 코드가 필요에 따라 탭 순서를 조정할 수 있다. 상태 보존 시스템은 탭의 위치를 기반으로 하지 않고 할당된 복원 식별자를 기반으로 탭의 콘텐츠를 복원한다. 


상태 보존 및 복원의 작동 방식에 대한 자세한 내용은 [App Programming Guide for iOS](https://developer.apple.com/library/archive/documentation/iPhone/Conceptual/iPhoneOSProgrammingGuide/Introduction/Introduction.html#//apple_ref/doc/uid/TP40007072)참조.


&nbsp;
## Differences in tvOS
탭 바 컨트롤러는 iOS에서와 같은 tvOS에서 동일한 목적을 수행하지만 약간 다른 사용자 인터페이스 기능을 제공한다.
* 탭 바 인터페이스가 창 상단에 나타난다. 포커스가 탭 바를 벗어나면 탭 바가 숨겨진다. 리모컨에서 위로 스와이프하면 탭 바가 다시 표시되고 포커스가 맞춰진다. 또한 사용자는 메뉴 버튼을 눌러 탭 바를 표시하고 포커스를 맞출 수 있다.
* 탭 바에서 아래로 스와이프하면 콘텐츠 뷰로 포커스가 이동한다. 구체적으로는 선택된 탭 아래의 시각적으로 첫 번째 포커스가 있는 뷰이다. 아래로 스와이프하는 것은 일반적인 포커스 변경 제스처처럼 동작한다. 즉 포커스가 사용자가 스와이프한 방향으로 이동한다. 선택한 탭 바로 아래에서 아무것도 포커스를 맞출 수 없는 경우 가장 가까운 포커스 가능 뷰가 대신 포커스를 맞춘다.
* 탭에 포커스를 맞추는 동안 선택 버튼을 누르면 콘텐츠 뷰로 포커스가 이동한다. 이 변경과 관련된 방향이 없기 때문에 콘텐츠 뷰의 [preferredFocusedView](https://developer.apple.com/documentation/uikit/uifocusenvironment/1616830-preferredfocusedview) 프로퍼티에 지정된 뷰로 포커스가 이동한다.
* tvOS의 탭 바 컨트롤러는 커스텀화를 지원하지 않는다. 탭 바 컨트롤러는 [viewControllers](https://developer.apple.com/documentation/uikit/uitabbarcontroller/1621185-viewcontrollers) 배열에서 화면에 맞는 뷰 컨트롤러 수만 표시하며 iOS에 표시되는 추가 인터페이스는 제공하지 않는다.


&nbsp;      
## Topics
### Customizing the Tab Bar Behavior
> 탭 바 동작 커스터마이징

* `var delegate: UITabBarControllerDelegate?`
    * 탭 바 컨트롤러의 델리게이트 객체이다.
* `protocol UITabBarControllerDelegate`
    * 탭 바의 동작을 커스터마이징하기 위해 구현하는 메서드 세트이다.
    

### Accessing the Tab Bar Controller Properties
> 탭 바 컨트롤러 프로퍼티에 접근하기

* `var tabBar: UITabBar`
    * 이 컨트롤러와 연결된 탭 바 뷰이다.
    

### Managing the View Controllers
> 뷰 컨트롤러 관리하기

* `var viewControllers: [UIViewController]?`
    * 탭 바 인터페이스에 의해 표시되는 루트 뷰 컨트롤러의 배열이다.
* `func setViewControllers([UIViewController]?, animated: Bool)`
    * 탭 바 컨트롤러의 루트 뷰 컨트롤러를 설정한다.
* `var customizableViewControllers: [UIViewController]?`
    * 이 탭 바 컨트롤러로 관리되는 뷰 컨트롤러의 하위 집합으로 커스터마이징 할 수 있다.
* `var moreNavigationController: UINavigationController`
    * 추가 내비게이션 인터페이스를 관리하는 뷰 컨트롤러이다.


### Managing the Selected Tab
> 선택된 탭 관리하기

* `var selectedViewController: UIViewController?`
    * 현재 선택된 탭 아이템과 연관된 뷰 컨트롤러이다.
* `var selectedIndex: Int`
    * 현재 선택된 탭 아이템과 연관된 뷰 컨트롤러의 인덱스이다.


&nbsp;
## Relationships
### Inherits From
* UIViewController


### Conforms To
* CVarArg
* Equatable
* Hashable
* NSCoding
* NSExtensionRequestHandling
* UIPasteConfigurationSupporting
* UIStateRestoring
* UITabBarDelegate
* UIUserActivityRestoring


&nbsp;
## See Also
### Tab View Interface
* class UITabBar
* class UITabBarItem


&nbsp;      
&nbsp;      
### [by. 0junChoi](https://github.com/0jun0815) email: <0jun0815@gmail.com>
### [Apple Developer Documentation UITabBarController](https://developer.apple.com/documentation/uikit/uitabbarcontroller)
