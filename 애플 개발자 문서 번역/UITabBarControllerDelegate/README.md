# UITabBarControllerDelegate
> 탭 바의 동작을 커스텀화하기 위해 구현하는 메서드 세트이다.


* [Overview](#overview)
* [Topics](#topics)
    * [](#)
* [Relationships](#relationships)
* [See Also](#see-also)


&nbsp;    
## Overview
탭 바의 동작을 보강하려는 경우 UITabBarControllerDelegate 프로토콜을 사용한다. 특히 특정 탭을 선택해야하는지 여부를 결정하거나, 탭을 선택한 후에 액션을 수행하거나 사용자가 탭 순서를 커스텀화하기 전이나 후에 액션을 수행하는 데 사용할 수 있다. 이러한 메서드를 커스텀 객체에 구현한 후에는 해당 객체를 해당 [UITabBarController](https://developer.apple.com/documentation/uikit/uitabbarcontroller) 객체의 [delegate](https://developer.apple.com/documentation/uikit/uitabbarcontroller/1621164-delegate) 프로퍼티에 할당해야 한다.


이 프로토콜의 모든 메서드는 선택 사항이다. 탭 바 컨트롤러와 그 델리게이트를 사용하고 구성하는 방법에 대한 자세한 내용은 [View Controller Programming Guide for iOS](https://developer.apple.com/library/archive/featuredarticles/ViewControllerPGforiPhoneOS/index.html#//apple_ref/doc/uid/TP40007457)를 참조한다.


&nbsp;      
## Topics
### Managing Tab Bar Selections
> 탭 바 선택 관리

* `func tabBarController(UITabBarController, shouldSelect: UIViewController) -> Bool`
    * 지정된 뷰 컨트롤러를 활성화할지 여부를 델리게이트에게 요청한다.
* `func tabBarController(UITabBarController, didSelect: UIViewController)`
    * 사용자가 탭 바에서 아이템을 선택했다고 델리게이트에게 알린다.


### Managing Tab Bar Customizations
> 탭 바 커스텀화 관리

* `func tabBarController(UITabBarController, willBeginCustomizing: [UIViewController])`
    * 델리게이트에게 탭 바 커스텀 시트가 표시될 예정임을 알린다.
* `func tabBarController(UITabBarController, willEndCustomizing: [UIViewController], changed: Bool)`
    * 델리게이트에게 탭 바 커스텀 시트가 해제될 예정임을 알린다.
* `func tabBarController(UITabBarController, didEndCustomizing: [UIViewController], changed: Bool)`
    * 델리게이트에게 탭 바 커스텀 시트가 닫혔음을 알린다.


### Overriding View Rotation Settings
> 뷰 회전 설정 재정의

* `func tabBarControllerSupportedInterfaceOrientations(UITabBarController) -> UIInterfaceOrientationMask`
    * 델리게이트가 탭 바 컨트롤러에 대해 지원되는 모든 인터페이스 방향을 제공 할 수 있도록하기 위해 호출되었다.
* `func tabBarControllerPreferredInterfaceOrientationForPresentation(UITabBarController) -> UIInterfaceOrientation`
    * 델리게이트가 탭 바 컨트롤러의 기본 설정 방향을 제공할 수 있도록 호출된다.


### Supporting Custom Tab Bar Transition Animations
> 커스텀 탭 바 전환 애니메이션 지원

* `func tabBarController(UITabBarController, animationControllerForTransitionFrom: UIViewController, to: UIViewController) -> UIViewControllerAnimatedTransitioning?`
    * 델리게이트가 비대화형 탭 바 뷰 컨트롤러 전환 중에 사용할 UIViewControllerAnimatedTransitioning 델리게이트 객체를 반환할 수 있도록 호출되었다.
* `func tabBarController(UITabBarController, interactionControllerFor: UIViewControllerAnimatedTransitioning) -> UIViewControllerInteractiveTransitioning?`
    * 델리게이트가 애니메이션 탭 바 전환 중에 사용할 UIViewControllerInteractiveTransitioning 델리게이트 객체를 반환할 수 있도록 호출되었다.


&nbsp;
## Relationships
### Inherits From
* NSObjectProtocol


&nbsp;
## See Also
### Customizing the Tab Bar Behavior
* var delegate: UITabBarControllerDelegate?


&nbsp;      
&nbsp;      
### [by. 0junChoi](https://github.com/0jun0815) email: <0jun0815@gmail.com>
### [Apple Developer Documentation UITabBarControllerDelegate](https://developer.apple.com/documentation/uikit/uitabbarcontrollerdelegate)
