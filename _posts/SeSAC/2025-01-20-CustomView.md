---
title:  "LoadView 이해하기"
excerpt: "iOS"

categories:
  - iOS
tags:
  - iOS
last_modified_at: 2025-01-20T13:40:02-05:00
---
### ViewController의 rootView를 customView로 교체하는 과정에 대하여..

# loadView
"Creates the view that the controller manages"<br><br>

ViewController는 기본적으로 view 프로퍼티를 통해서 루트뷰를 관리한다.<br>
하지만 처음부터 view가 메모리에 생성되어있는 것이 아니다.(필요한 시점에 생성)<br>
<br>
처음에 view 프로퍼티에 접근해서 ViewController.view가 메모리에 있는지 확인한다.<br>
만약 아직 뷰가 없다면 loadView 메서드를 호출해서 뷰를 생성한다. 
<br><br>

**loadView가 호출될 때 뷰 생성 방식**<br>
제일 먼저 사용자가 override loadView를 했다면 해당 메서드 내에서 뷰를 생성.<br>
사용자가 loadView를 override를 하지 않았다면 인터페이스 빌더에서 Xib파일이 있는지 확인.<br>
(override와 xib 파일 모두 있다면 xib파일은 무시된다.)
아무것도 없을 경우 빈 UIView 객체를 생성해서 view 프로퍼티에 할당.<br><br>

**만약 사용자가 loadView를 override 했지만 뷰를 할당하지 않았다면?**<br>

```swift
class MyViewController: UIViewController {
    override func loadView() {
    }
}

let vc = MyViewController()
print(vc.view) // crash 발생!
```
view는 nil이 되어 앱이 크래시. loadView를 오버라이드 할 경우 반드시 self.view를 초기화 해야한다.
<br><br>

### Your custom implementation of this method should not call super.

super.loadView는 스토리보드의 뷰를 로드하려고 하는데 만약 customView를 사용할 경우
super.loadView를 호출 할 경우 충돌이 발생할 수 있다. 생각해보면 딱히 필요없다. override해서 새로 self.view를 정의할 것이기 때문.
<br><br>


### UIview와 CustomView

```swift
class ViewController: UIViewController {
    var mainView = CustomView()

    override func loadView() {
        self.view = mainView
    }

    override func viewDidLoad() {
        super.viewDidLoad()
        mainView.titleLabel = "title" 
        // self.view.titleLabel = "title" // 에러 발생
    }
}
```
loadView에서 view를 mainView로 할당했는데 왜 viewDidLoad에서 mainView를 통해 titleLabel에 접근해야 하는 것일까?<br><br>

- self.view의 타입은 UIView 타입으로 UIView에 정의된 프로퍼티와 메서드에만 접근할 수 있다.
- mainView의 타입은 CustomView타입으로 커스텀 프로퍼티와 메서드에 접근할 수 있다.<br>

따라서 CustomView의 프로퍼티에 접근하려면 타입 캐스팅을 해야한다.<br>

```swift
(self.view as? CustomView)?.titleLabel
```
<br><br>

UIButton, UILabel, CustomView 등은 모두 UIView를 상속받는다.<br>
따라서 UIViewController는 모든 뷰의 기반인 UIView를 통해 뷰를 관리한다.<br>
UIViewController의 viewDidLoad, viewWillAppear 등은 UIView 타입을 기반으로 작동한다.<br>
뷰를 관리하기 위해서 어떤 뷰가 들어와도 처리할 수 있도록 UIView로 타입을 제한해야할 필요가 있다.<br>

<br>

```swift
override func viewDidLoad() {
    super.viewDidLoad()
    mainView.backgroundColor = .red
    view.backgroundColor = .blue
}
```

- mainView는 UIView를 상속받고 있기 때문에 mainView.backgroundColor = .red로 배경색이 빨간색으로 설정.
- loadView에서 self.view를 mainView로 할당하면서 둘은 동일한 객체를 참조하게 된다.
  - (view.backgroundColor = .blue) == (mainView.backgroundColor = .blue)로 같은 결과가 된다.
- 결과적으로 배경색은 파란색으로 설정된다.

