---
title:  "Opaque Type 이해하기"
excerpt: "iOS"

categories:
  - iOS
tags:
  - iOS
last_modified_at: 2025-04-17T01:40:02-05:00
---

# Opaque Type

Opaque란 불투명한, 불분명한 이라는 뜻이다.<br>
그렇다면, OpaqueType이라는 것은 불투명한, 불분명한 타입이라는 것이다..!<br><br>
불분명하다는 것은 분명하지 않다는 것. -> 분명하지 않다는 것은 뭐 하나 딱 집어 정의할 수 없다는 것이다.<br><br>

![Image](https://github.com/user-attachments/assets/da14a2ee-948a-45f6-b174-ae41c6782536)

<br>
사과만 있는 바구니는 보고 '사과 바구니'라고 딱 말할 수 있지만, 사과, 포도, 수박,, 등이 들어있는 바구니를 보고 '사과 바구니' , '포도 바구니'라고 딱 집어 말할 수 없어서 '과일 바구니'라고 부르는 것처럼..!<br><br>

그래서 Opaque Type을 사용하는 입장에서는 바구니를 하나하나 따지면서, 사과, 포도, 수박 등이 들어있는 바구니 가 아니라 과일바구니로 취급하고 사용하겠다는 것이다.<br>
여기서 놓지면 안되는 점, 누군가는 과일 바구니에 구체적으로 뭐가 들어있는지 알고 있다.<br>
당연히 바구니 제작자!<br><br>

제작자는 알고, 사용자는 모른다 -> `구현부`는 알고, `호출부`는 모른다.<br> <br>
이 개념이 Opaque Type의 핵심이다.<br>
`some` 키워드를 통해 함수가 반환하는 구체 타입을 숨기고, 프로토콜만 노출시키기 때문에 호출자는 반환 타입의 구체적인 정보를 알 수 없다. 이는 마치 과일 바구니가 과일을 가지고 있다는 것은 알지만 그 안에 어떤 과일이 들어있는지는 정확히 모른 채로 사용하는 것과 같다.<br><br>

여기서 opaque Type이 역제네릭이라는 의미에 대해 알 수 있다.

## Opaque Type은 역제네릭이다.
Opaque Type : `구현부`는 알고, `호출부`는 모른다.<br>
Generic Type:  `호출부`는 알고, `구현부`는 모른다.

```Swift
protocol Human {
    var name: String { get }
    var age: Int { get }
}
struct Jann: Human {
    let name: String = "Jann"
    let age: Int = 17
    let learn: String = "ios"
}
struct Kann: Human {
    let name: String = "Kann"
    let age: Int = 18
    let teach: String = "andriod"
}

// QpaqueType
func getJann() -> some Human {
    return Jann() // 구현부: Jann
}
let who = getJann() // 호출부: Human(Protocol)

//Generic Type
func getSum<T: AdditiveArithmetic>(a: T, b: T) -> T {
    return a + b // 구현부: AdditiveArithmetic(Protocol)
}
let what = getSum(a: 1, b: 2) // 호출부 : Int
```


- Generic : 호출부가 타입을 넘겨주고 구현부는 그 Protocol에 맞게 동작
- Opaque : 구현부가 타입을 정하고 호출부는 some Protocol로 동작


## 왜 호출부는 Protocol로 반환받아야 할까?

구체적이라는 건, 사용 범위가 그만큼 좁아진다고 볼 수 있다.

```swift
func makeJann() -> Jann {
    return Jann()
}

let person = makeJann()
print(person.name)
print(person.learn) // Jann만 가지고 있는 것
```
이렇게 구체 타입으로 반환하여 사용하면 사용자는 Jann에 의존해서 코드를 짜게 된다.<br>
즉, Jann이어야만 작동되는 코드가 짜여질 가능성이 크다.<br><br>

**만약 Jann이 아니라 다른 타입을 사용하고 싶다면?**

```swift
struct SuperJann: Human {
    let name = "SuperJann"
    let age = 25
    let superPower = "SwiftUI"
}

func makeJann() -> SuperJann {
    return SuperJann()
}

let person = makeJann()
print(person.name)
print(person.learn) // 에러에러
```
그 전까지 Jann 중심으로 작성된 코드가 모두 깨져버리게 된다.<br><br>

**Opaque Type으로 쓴다면?**

```swift
func makeHuman() -> some Human {
    return Jann()
}
let human = makeHuman()
print(human.name) // 가능
print(human.age) // 가능
func makeHuman() -> some Human {
    return SuperJann()
}
print(human.name) // 완전가능
print(human.age) // 완전가능
```

유연하게 캡슐화가 가능하다!!<br><br>

여기서 SwiftUI를 쓰는 사람이라면 바로 이해가 갈 `body`!<br>

```swift
struct testOpaque: View {
    var body: some View {
        Text("Hello, World!")
        Text("Hello, World!")
        Text("Hello, World!")
        Image(systemName: "star")
        
    }
}
```
이렇게 간단한 코드도 들어다보면..<br>
body type: `TupleView<(Text,Text,Text,Image)>` 간단하지 않은 타입이다.<br><br>
만약 modifier라도 추가하게되면..<br>

```swift
struct testOpaque: View {
    var body: some View {
        Text("Hello, World!")
            .background(.red)
        Image(systemName: "star")
            .frame(width: 100, height: 100)
        
    }
}
```
body type: `TupleView<(ModifiedContent<Text, _BackgroundModifier<Color>>, ModifiedContent<Image, _FrameLayout>)>`더 간단하지 않은 타입이 된다.
<br><br>

이렇게 body에 view를 추가 할 때마다 body가 사용되는 모든 코드를 수정해 주기 힘들기 때문에 body에서 opaque type을 쓰는 것은 좋은 선택이다.<br>


## 왜 구현부는 구체타입을 알아야 해?
`"구현부에서 어떤 구체 타입을 리턴하는지 명확해야 컴파일러가 정적 타입 시스템의 장점을 활용할 수 있다"`<br><br>

결론부터 말하면, Swift의 장점을 최대한 활용하기 위해!<br><br>

정적 타입(Static typing) 언어란 변수와 표현식의 타입이 컴파일 시점에 결정되고 검사되는 프로그래밍 언어를 말한다. `Swift는 정적 타입 언어`이다.<br>
정적 타입 언어의 특징에 '컴파일 시점에 미리 타입이 결정되어 있어야 한다.'가 있음!<br><br>

컴파일러가 구체 타입을 알면 타입 불일치 오류를 미리 감지할 수 있다. 예를 들어 문자열에 정수를 더하려는 시도를 미리 알고 잡아낼 수 있다.<br>
더하여...
- 어떤 메모리 크기를 할당해야 하는지
- 어떤 메서드를 호출할 수 있는지
- 최적화를 어떻게 할 수 있는지
컴파일러가 판단 가능!!


### Swift의 타입 추상화 방식: OpaqueType & Boxed Protocol
some(Opaque Type)과 any(Boxed Protocol) - 이 두 방식은 구현의 세부사항을 숨기면서 Swift의 정적 타입 시스템을 다르게 활용한다.<br><br>

**Opaque Type**
```swift
func makeHuman1(condition: Bool) -> some Human {
    if condition {
        return Jann()  // 구현부: Jann
    } else {
        return Kann()  // 구현부: Kann
    }
}
```
- 메서드가 반환하는 구체적인 타입을 컴파일러가 알고 있어야 하기때문에, 위 코드는 컴파일 오류가 발생한다. 
- `some Human` -> `단일 구체 타입 반환`

<br><br>

**Boxed Protocol**
```swift
func makeHuman2(condition: Bool) -> any Human {
    if condition {
        return Jann()  // 구현부: Jann
    } else {
        return Kann()  // 구현부: Kann
    }
}
```
- `any`는 프로토콜을 따르는 어떤 타입이든 `box`에 담아서 처리한다.(Type Eraser)
- `any Human`을 사용하면 Human 프로토콜을 따르는 서로 다른 타입을 반환할 수 있다.
- 하지만 타입 정보를 숨기기 때문에, 런타임 때 타입을 확인애햐 하는 오버헤드가 발생!
- -> 컴파일러가 정적 타입 시스템의 이점을 활용하기 어려워진다.