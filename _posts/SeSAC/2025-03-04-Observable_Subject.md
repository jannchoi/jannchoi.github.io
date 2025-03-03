---
title:  "Observable과 Subject"
excerpt: "iOS"

categories:
  - iOS
tags:
  - iOS
last_modified_at: 2025-03-04T07:40:02-05:00
---


## Observable의 한계
- Observable은 데이터를 방출 할 수 있지만, 초기에 생성되고나면 이후 이벤트를 추가할 수 없다.
  - 즉, 초기화 시점에 방출할 데이터를 지정해야 한다.
- 새로운 구독자가 생길 때마다 새로운 Observable이 실행된다. 

```swift
let sampleInt = Observable<Int>.create { observer in
    observer.onNext(Int.random(in: 1...100)) 
    return Disposables.create()
}
sampleInt.subscribe { value in
    print("1", "\(value)") // 1 23
}.disposed(by: disposeBag)

sampleInt.subscribe { value in
    print("2", "\(value)") // 2 76
}.disposed(by: disposeBag)

sampleInt.subscribe { value in
    print("3", "\(value)") // 3 45
}.disposed(by: disposeBag)
```
- 모두 같은 'sampleInt'를 구독하지만 각각 다른 값을 받아 출력되는것을 알 수 있다.

## Subject
Observable의 한계를 극복하기 위해 나타난 것.<br>
- Observable처럼 데이터를 방출할 수도 있지만, Observer처럼 이벤트를 전달할 수도 있다. 
- 여러 구독자가 동일한 데이터를 받을 수 있다.

```swift
let sampleInt = BehaviorSubject(value: 0) 
sampleInt.onNext(Int.random(in: 1...100)) 

sampleInt.subscribe {
    value in
    print("1", "\(value)") // 1 42
}.disposed(by: disposeBag)

sampleInt.subscribe {
    value in
    print("2", "\(value)") // 2 42
}.disposed(by: disposeBag)

sampleInt.subscribe {
    value in
    print("3", "\(value)") // 3 42
}.disposed(by: disposeBag)
```
- 각 구독자는 동일한 값을 받아 출력된다. 

### Subject의 종류

#### PublishSubject
초기값이 없는 상태로 시작한다. 그래서 구독 이전에 정의된 이벤트는 무시한다.<br>

```swift
func testPublishSubject() {
    let subject = PublishSubject<Int>()
    
    subject.onNext(2) // 구독자X -> 무시
    subject.onNext(5) // 구독자X -> 무시
    
    subject.subscribe(with: self) { owner, value in
        print(#function, value)
    } onError: { owner, error in
        print(#function, error)
    } onCompleted: { owner in
        print(#function, "oncompleted")
    } onDisposed: { owner in
        print(#function, "onDisposed")
    }.disposed(by: disposeBag)
    
    subject.onNext(7)
    subject.onNext(10)
    subject.onNext(20)
    subject.onCompleted()
    subject.onNext(45) // completed -> disposed -> emit안됨.
    subject.onNext(56) // completed -> disposed -> emit안됨.
}

/*
출력 예시:
testPublishSubject 7
testPublishSubject 10
testPublishSubject 20
testPublishSubject oncompleted
testPublishSubject onDisposed
*/
```

#### BehaviorSubject
초기값이 있는 상태로 시작한다. 구독 이전 가장 최근에 정의도니 이벤트를 전달받는다.<br>

```swift
func testBehaviorSubject() {
    let subject = BehaviorSubject(value: 1) // 🎯 초기값 1
    
    subject.onNext(2) // 최근 값: 2
    subject.onNext(5) // 최근 값: 5
    
    subject.subscribe(with: self) { owner, value in
        print(#function, value)
    } onError: { owner, error in
        print(#function, error)
    } onCompleted: { owner in
        print(#function, "oncompleted")
    } onDisposed: { owner in
        print(#function, "onDisposed")
    }.disposed(by: disposeBag)
    
    subject.onNext(7)
    subject.onNext(10)
    subject.onNext(20)
    subject.onCompleted()
    subject.onNext(45) // completed -> disposed -> emit안됨.
    subject.onNext(56) // completed -> disposed -> emit안됨.
}

/*
출력 예시:
testBehaviorSubject 5
testBehaviorSubject 7
testBehaviorSubject 10
testBehaviorSubject 20
testBehaviorSubject oncompleted
testBehaviorSubject onDisposed
*/
```

### ReplaySubject
정의된 buffer만큼의 이벤트를 가지고 있다가 구독 이후 한번에 emit한다.<br>

```swift
func testReplaySubject() {
    let subject = ReplaySubject<Int>.create(bufferSize: 2) // 최근 2개의 값 저장
    
    subject.onNext(1) // [1]
    subject.onNext(2) // [1, 2]
    subject.onNext(3) // bufferSize = 2 -> [2, 3] 
    
    subject.subscribe(with: self) { owner, value in
        print(#function, value)
    } onError: { owner, error in
        print(#function, error)
    } onCompleted: { owner in
        print(#function, "oncompleted")
    } onDisposed: { owner in
        print(#function, "onDisposed")
    }.disposed(by: disposeBag)
    
    subject.onNext(4)
    subject.onNext(5)
    subject.onCompleted()
}

/*
출력 예시:
testReplaySubject 2 
testReplaySubject 3
testReplaySubject 4
testReplaySubject 5
testReplaySubject oncompleted
testReplaySubject onDisposed
*/
```


#### AsyncSubject
onCompleted직전에 정의된 이벤트를 onCompleted 시점에 emit한다.<br>

```swift
func testAsyncSubject() {
    let subject = AsyncSubject<Int>()
    
    subject.onNext(1)
    subject.onNext(2)
    subject.onNext(3)
    
    subject.subscribe(with: self) { owner, value in
        print(#function, value)
    } onError: { owner, error in
        print(#function, error)
    } onCompleted: { owner in
        print(#function, "oncompleted")
    } onDisposed: { owner in
        print(#function, "onDisposed")
    }.disposed(by: disposeBag)
    
    subject.onNext(4)
    subject.onNext(5)
    subject.onCompleted()
}

/*
출력 예시:
testAsyncSubject 5 // 마지막 값만 전달
testAsyncSubject oncompleted
testAsyncSubject onDisposed
*/
```
- onNext(5)이전까지의 이벤트는 받지만 방출이 되지 않는다.
- onCompleted()가 호출되는 즉시 마지막 값인 5가 방출된다.