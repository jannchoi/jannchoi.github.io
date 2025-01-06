---
title:  "ViewController LifeCycle 이해하기"
excerpt: "iOS"

categories:
  - iOS
tags:
  - iOS
last_modified_at: 2025-01-05T13:40:02-05:00
---

# ViewControlle LifeCycle

![R1280x0](https://github.com/user-attachments/assets/89e5ff68-6a86-48da-96fd-31e98284ce7d)

## ViewController LifeCycle 개념
뷰가 화면에 나타나고 사라지는 일련의 과정.

### 1. loadView
뷰 컨트롤러의 뷰를 생성하고 메모리에 올린다.


### 2. viewDidLoad
뷰 컨츠롤러의 뷰가 메모리에 올라온 후 호출된다.<br>
화면이 나타나기 직전에 초기화 해줄 뷰가 있다면 이때 진행한다. **root view일 경우 최초 한번만 호출된다.** 
따라서 데이터가 변경되어야 하는 경우 이떄 실행하면 안된다. 


### 3. viewWillAppear
뷰가 화면에 나타날 것이라는 것을 뷰컨트롤러에 알린다.(뷰가 화면에 나타나기 직전)<br>
화면에 표시되기 직전에 필요한 작업을 수행한다. viewDidLoad와 다르게 화면이 전환될 때마다 호출되기 때문에 화면이 전환될 때 변경되어야 할 값이 있으면 이때 실행한다.


### 4. viewDidAppear
뷰가 화면에 나타났다는 것을 뷰컨트롤러에 알린다.(뷰가 화면에 완전히나타난 직후)<br>
뷰가 화면에 나타난 이후 애니메이션이 시작되어야 할 때 작업할 수 있다.

### 5. viewWillDisappear
뷰가 화면에서 제거될 것이라는 것을 뷰켠트롤러에 알린다.(뷰가 사라지기 직전)<br>
화면이 사라지기 전 상태를 저장하거나 리소스를 해제하는 작업을 할 수 있다.<br>
뷰가 사라진다 != 메모리에서 해제된다.

### 6. viewDidDisappear
뷰가 화면에서 사라졌다는 것을 뷰컨트롤러에 알린다.(뷰가 사라진 직후)<br>
화면이 사라지면서 멈춰야 할 작업이 있다면 이때 멈춰준다.
<br><br>


## 화면 전환 별 메서드 호출 순서
메서드 호출 순서를 관찰하기 위해 구성된 뷰는 총 네개.<br>
- rootView
- modalView
- showView
- 2ndTabView: tab bar controller

### rootView 

RootView viewDidLoad()<br>
RootView viewWillAppear(_:)<br>
RootView viewDidAppear(_:)<br>

### modalView
1) modalView 호출

ModalView viewDidLoad()<br>
RootView viewWillDisappear(_:)<br>
ModalView viewWillAppear(_:)<br>
ModalView viewDidAppear(_:)<br>
RootView viewDidDisappear(_:)<br>

- modalView가 다시 메모리에 올라가서 viewDidLoad가 호출되는 것을 알 수 있다.
<br><br>

2) modalView -> RootView

unwindToPracticeViewController(_:) // rootview로 되돌아가기 위한 메서드<br>
ModalView viewWillDisappear(_:)<br>
RootView viewWillAppear(_:)<br>
RootView viewDidAppear(_:)<br>
ModalView viewDidDisappear(_:)<br>

- rootView의 viewDidLoad는 다시 실행되지 않는 것을 알 수 있다. rootview는 여전히 뷰 계층구조에 남아있지만 비활성화 상태이다.
<br><br>

- modalview는 기존의 RootView 위에 새로운 modalView를 덮어서 표시한다. 
- Rootview가 완전히 가려진 이후 viewDidDisappear가 호출된다.

### showView

1) showView 호출

ShowView viewDidLoad()<br>
RootView viewWillDisappear(_:)<br>
ShowView viewWillAppear(_:)<br>
RootView viewDidDisappear(_:)<br>
ShowView viewDidAppear(_:)<br>
<br><br>

2) showView -> RootView

ShowView viewWillDisappear(_:)<br>
RootView viewWillAppear(_:)<br>
ShowView viewDidDisappear(_:)<br>
RootView viewDidAppear(_:)<br>
<br><br>

- Navigation Stack을 사용해 뷰를 관리하기 때문에 새로운 뷰가 활성화되기 전에 기존 뷰가 완전히 비활성화된다.

**Modal과 Show의 생명주기 호출 순서가 다르다!**
<br><br>

### Tab bar

1) 2ndTabView

2ndTabView viewDidLoad()<br>
2ndTabView viewWillAppear(_:)<br>
RootView viewWillDisappear(_:)<br>
2ndTabView viewDidAppear(_:)<br>
RootView viewDidDisappear(_:)<br>
<br><br>

2) 2ndTabView -> rootview

RootView viewWillAppear(_:)<br>
2ndTabView viewWillDisappear(_:)<br>
RootView viewDidAppear(_:)<br>
2ndTabView viewDidDisappear(_:)<br>
<br>


- tabBar의 경우에 **rootview처럼 ViewDidLoad가 최초 한번 실행되면 다시 실행되지 않는다. **
- 2ndTabView도 한번 메모리에 올라가면 다시 다른 뷰로 옮겨도 **메모리에서 해제되지 않는다.**




