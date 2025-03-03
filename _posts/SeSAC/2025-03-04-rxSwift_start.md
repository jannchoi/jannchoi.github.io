---
title:  "RXSwift와 가까워지기"
excerpt: "iOS"

categories:
  - iOS
tags:
  - iOS
last_modified_at: 2025-03-04T01:40:02-05:00
---

# RXSwift 필요성 느끼기
rxSwift가 가장 필요한 순간은 비동기적 이벤트가 일어날 떄이다.<br>
비동기와 관련된 여러 작업, Closure, GCD, Delegate, Notification Center를 떠올려 보자.<br>
- **Closure** : 작업이 끝나면 호출되어 그 결과를 처리.
- **GCD**: 특정 작업을 백그라운드에서 실행하고 결과를 메인 스레드에서 처리.
- **Delegate**: 작업이 완료되면 다른 객체에 알려 결과를 처리.
- **Notification Center**: 특정 이벤트를 발생시키면 여러 객체가 이벤트를 받아 비동기적으로 처리.

<br>
이들을 조금 멀리 떨어져서 하나의 덩어리로 바라볼 수 있을까?<br><br> 

다시 한번 비동기 작업을 바라보는 데, 각 작업을 버튼이라고 생각해 보자.
- **Closure** : 작업이 끝나면(버튼을 클릭) 호출되어 그 결과를 처리.
- **GCD**: 특정 작업을 백그라운드에서 실행하고(버튼을 클릭) 결과를 메인 스레드에서 처리.
- **Delegate**: 작업이 완료되면(버튼을 클릭) 다른 객체에 알려 결과를 처리.
- **Notification Center**: 특정 이벤트를 발생시키면(버튼을 클릭) 여러 객체가 이벤트를 받아 비동기적으로 처리.

어떤 시점에 몇 번 일어날 지 모르는 이벤트가 발생할 때 그때그때 이벤트를 받아 처리한다.<br><br>

여러 비동기 작업을 버튼 클릭 작업으로 바라보았던 것처럼,<br>
이 패러다임을 비동기적으로 일어날 수 있는 여러 작업에 일관된 방식으로 적용시켜 보자는 것이 핵심이다. 
<br><br>

# RXSwift의 핵심 주체 알아보기
RxSwift의 흐름을 이해하려면 **Observable**, **Observer**, **Subscribe**, **Dispose** 네 가지 개념을 알아야한다.<br>


### Observable
관찰 가능한 이라는 뜻으로 해석될 수 있다.<br>
비동기 작업, 즉 버튼이 클릭되었다는 것을 알아치리고, 이벤트를 **item**으로 **emit(방출)** 한다. <br>
이때 emit을 **sequence** 형태로 방출한다고 표현한다. 즉 버튼이 여러 번 눌리면 눌리는 '순서대로' item을 방출한다는 것이다.<br>
= 데이터를 흘려 보낸다.

### Observer
Observable이 item을 emit했을 때. 해당 item을 받아서 처리하는 주체이다.<br>
- **Observer**없이 **Observable**만 있다면? -> **버튼을 클릭**했는데 **액션**이 없다면? -> 아무 일도 일어나지 않는다.(그냥 버튼 클린한 사람 됨.)<br>
즉 Observable이 방출한 item에 대한 처리를 정의한다. <br>
= Observable이 흘러보낸 데이터를 구독한다.

### Subscribe
Observable이 방출한 item을 Observer가 받을 수 있도록 연결한다. <br>
= Observer가 Observable의 데이터 흐름을 구독할 수 있도록 한다.<br>
- Observable에게 나중에 Observer가 생긴다면? -> 나중에 Subscribe를 통해 Observer가 구독했다면? -> 구독을 한 시점부터 item을 받아 액션을 실행한다.<br>


### Dispose
처분하다.<br>
Observable가 방출하는 item을 Observer가 더이상 받고 싶지 않다면 **구독을 해지**해야 한다. 이때 호출하는 것이 'dispose()'메서드 이다.<br>
= Observable과 Observer 사이의 데이터 흐름을 끊는다.


### RXSwift 예제 코드

```swift
button.rx
        .tap
        .subscribe(onNext: {
            print("버튼이 클릭되었습니다.")
        },
        onError: { error in
            print("에러가 발생했습니다.")
        },
        onCompleted: {
            print("이벤트가 끝났습니다.")
        },
        onDisposed: { owner in
            print("구독이 해지되었습니다.")
        }).disposed(by: disposeBag)
```
- **onNext** : item이 emit되었을 때 실행 할 클로저.
- **onError** : 에러가 발생했을 때 실행 할 클로저.
- **onCompleted** : 이벤트가 완료되었을 때 실행 할 클로저.
- **onDisposed** : 구독이 해지되었을 때 실행 할 클로저.