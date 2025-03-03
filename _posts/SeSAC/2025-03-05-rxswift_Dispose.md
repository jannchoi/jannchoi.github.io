---
title:  "Dispose이해하기"
excerpt: "iOS"

categories:
  - iOS
tags:
  - iOS
last_modified_at: 2025-03-05T01:40:02-05:00
---

# Dispose
Observer의 Observable에 대한 구독을 해지하고 싶을 때 호출하는 dispose()메서드...

subscribe의 반환값이 Disposable타입. 
Disposable 프로토콜의 메서드가 Dispose

```swift
nextButton.rx.tap.subscribe { _ in
            print("버튼이 클릭되었습니다.")
        }.dispose()
```
- 버튼을 클릭하고 나면 dispose메서드 호출로 인해서 구독이 끊어진다. -> 그 이후로는 버튼을 아무리 많이 클릭해도 print문이 출력되지 않는다. 
**문제1 버튼의 기능이 일회성으로 작동한다.**
<br><br>

```swift
// in SecondVC
 let timer = Observable<Int>.interval(.seconds(1), scheduler: MainScheduler.instance)

 timer
        .subscribe(with: self) { owner, value in
            print("Timer", value)
        }
```
- rootVC에서 SecondVC를 present했을 떄 timer가 동작한다고 가정하자.
- SecondVC가 present된 순간부터 1초마다 print문이 실핼될 것이다.
- 이때 만약 SecondVC를 dismiss한다면 여전히 타이머가 작동되어 숫자가 출력된다. 
- SecondVC가 사라지고 Timer Observable도 사라졌는데 timer는 여전히 작동한다.
-> **문제2 메모리 릭이 발생**

<br><br>

#### 해결방안1
**문제1과 문제2를 해결하기 위해 ViewController가 살아있는 동안 Observer가 활성화되어있도록 하면서, ViewController가 deinit되면 Observer에 대한 리소스도 사라지도록 하자**<br>
- 여기서 발생할 수 있는 **new문제점: 모든 Observer마다 dispose를 시켜주면 코드가 끝도 없이 늘어난다.**

```swift
class ViewController: UIViewController {


    deinit {
       timer1.dispose()
       timer2.dispose()
       timer3.dispose()
       ...
       timer100.dispose()
    }
}
```
<br><br>


#### 해결방안2
**DisposeBag**을 이용하자.<br>

```swift
class ViewController: UIViewController {
    let disposeBag = DisposeBag()

    override func viewDidLoad() {
        super.viewDidLoad()
        signUpButton
            .rx
            .tap
            .bind { _ in 
            print("signUpbutton 클릭되었음")
        }.disposed(by: disposeBag)

        emailText
            .subscribe { value in
                print("emailText onNext")
            }.disposed(by: disposeBag)

        signUpTitle.bind(to: signUpButton.rx.title()).disposed(by: disposeBag)

        signUpTitleColor.bind(with: self) { owner, value in
            owner.signUpButton.setTitleColor(value, for: .normal)
        }.disposed(by: disposeBag)
    }
}
```
- disposeBag은 dispose할 객체를 한번에 담아서 보관한다.
- disposeBag객체가 살아있는 한 signUpButton, emailText 등은 모두 활성화가 되어 있고, ViewController가 deinit되면서 내부 프로퍼티인 disposeBag도 deinit되면 disposeBag에 담겨있는 모든 객체도 메모리에서 해제된다.

### 몇 가지 짚고 넘어가야 할 사항
- **complete** or **error** **->** **dispose** (O)
- **subscribe** **->** **dispose** (X)
- **dispose** **->** **error** or **complete** (X)
- **dispose** **->** **disposeBag deinit** (X)
- **disposeBag deinit** **->** **dispose** (O) (해당 객체를 dispose bag에 담아두었을 경우)
- **dispose != event 종료**