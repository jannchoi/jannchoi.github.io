---
title:  "NavigationController와 TabBarController의 관계"
excerpt: "iOS"

categories:
  - iOS
tags:
  - iOS
last_modified_at: 2025-02-04T13:10:02-05:00
---

# NavigationController와 TabBarController의 관계

**직렬적으로 화면 전환이 이루어지던 navigationController에 tabbarController가 들어가게되면 어떻게 될까?**

<br><br>

## Container View Controller
뷰 컨트롤러를 관리하는 뷰 컨트롤러로서, 여러 뷰 컨트롤러 사이에서 화면전환을 담당한다. <br>
Container View Controller의 종류에 UINavigationController, UITabBarController 등이 있다.<br>
<br>

## UINavigationController
여러 뷰 컨트롤러를 navigationStack으로 관리한다<br>
**push**를 하게 되면 navigationStack에 뷰 컨트롤러가 추가되고,<br>
**pop**을 하게 되면 navigationStack에 뷰 컨트롤러가 제거된다. <br>
<br>

## UITabBarController
여러 뷰 컨트롤러를 탭 형식으로 관리한다<br>
navigationController와 다르게 독립적으로 뷰컨트롤러를 관리한다.<br>
<br>

### navigationController에 tabbarController가 추가되는 경우

```swift
window?.rootViewController = UINavigationController(rootViewController: RootViewController())
```

![Image](https://github.com/user-attachments/assets/213d0139-1382-4a9d-9b07-5bb8fa9c5c1f)
<br>
- RootVC에서 secondVC로 push되는 경우 navigationStack에 secondVC가 추가된다.
- 만약 secondVC에서 ThirdVC로 탭이 변경되는 navigationStack에서 secondVC가 pop되는 동시에 ThirdVC가 push된다.
- 하나의 navigationStack에 여러 뷰가 push되고 pop되는 것을 알 수 있다.
<br><br>

## tabbarController의 각 탭이 navigationController를 가지는 경우

```swift
window?.rootViewController = TabBarController()
```

```swift
// TabBarController
let firstTab = UINavigationController(rootViewController: FirstViewController()) 
let secondTab = UINavigationController(rootViewController: SecondViewController())
```

![Image](https://github.com/user-attachments/assets/17c1353d-1673-4ac0-a406-afb4da0ce556)
<br>
- TabBarController에서 독립적인 UINavigationController를 만들었기 때문에 두 개의 navigationStack이 생성된다.
- 각각의 navigationStack에 FirstVC, FourthVC가 push되는 것을 알 수 있다.
<br><br>

##  UITabBarController 내부의 viewControllers는 초기에는 navigationController가 없다?!
위 두 가지 실험 중 각 뷰 컨트롤러의 viewDidLoad와 viewWillAppear에 print(self.navigationController)를 출력해보았다.<br>
RootVC에서 tabbarController로 push되는 경우에만, tabbarController의 첫번째 뷰인 secondVC의 viewDidLoad에는 navigationController가 nil이었고,
 viewWillAppear시점에 정상적으로 navigationController가 출력되었다.<br>

<img width="405" alt="Image" src="https://github.com/user-attachments/assets/b2742a18-9d2a-449d-a417-49c34ef73f49" />
<br><br>
viewDidLoad는 navigationController와 무관하게 viewController가 view를 참조할 때 호출된다.<br>
그래서 viewDidLoad시점에 해당 viewController가 navigationStack에 push되었다는 보장이 없다.<br>
<br>

**왜 다른 viewControllers와는 다르게 tabbarController가 push될 때 navigationController의 연결 시점이 늦는 것일까?**

<img width="423" alt="Image" src="https://github.com/user-attachments/assets/c15ab7dc-5e0e-41fc-b247-74b352eb11dd" />
<br><br>
tabbarController의 viewWillAppear는 자식 뷰 컨트롤러인 secondVC가 화면에 나타날 준비가 되었을 떄 호출된다.<br>
secondVC가 로드되고 tabbarController가 navigationController와 연결되면서,<br>
tabbarController의 자식인 secondVC도 navigationController에 연결되어서 viewWillAppear 시점에서야 navigationController를 확인할 수 있게 된다.