---
title:  "Value Type & Reference Type 이해하기"
excerpt: "iOS"

categories:
  - iOS
tags:
  - iOS
last_modified_at: 2025-01-19T13:40:02-05:00
---

# Value Type과 Reference Type을 구조체와 클래스로 설명

값타입과 참조타입의 대표 에시인 구조체와 클래스로 설명해 보겠다.<br>

![Image](https://github.com/user-attachments/assets/d9c20440-2825-4d90-b84c-3a4bf5e8f1a1)
<br>
구조체는 값타입이기 때문에 인스턴스와 프로퍼티가 모두 stack에 저장된다. <br>
Han을 Dan으로 할당하면 새로운 공간에 같은 Dan과 똑같은 값이 복사된다. <br>
<br><br>

![Image](https://github.com/user-attachments/assets/9cf02074-c43c-4fa0-b51b-aa749a8563e0)
<br>
반면, 클래스는 참조타입이기 때문에 인스턴스의 값은 Heap에 저장되고 주솟값만 Stack에 저장된다.<br>
Han을 Dan으로 할당하면 새로운 stack공간에 Dan의 Heap 주소를 가지게 된다. 주솟값만 복사된다.<br>
**Han과 Dan이 같은 주소를 가리키고 있기 때문에** 만약 Han의 ID를 "H"로 바꾼다면 Heap의 101번 주소에 있는 ID가 "H"로 바뀌게 되고,
 Han.Id == "H"가 되며 Dan.Id == "H"가 된다.
<br><br>

#### 만약 인스턴스를 상수로 정의하면 어떻게 될까?

![Image](https://github.com/user-attachments/assets/2f496ea9-f861-4baf-9593-07e2f162487d)
<br>
구조체의 경우 Stack영역에 저장된 값이 잠금되어 프로퍼티를 변경할 수 없다.<br>
<br><br>

![Image](https://github.com/user-attachments/assets/01ff04f6-705d-494b-a92c-d300f863da7b)
<br>
반면 클래스의 경우 프로퍼티 값을 변경할 수 있다. Stack 영역에 저장된 값을 변경시키는 것이 아니라 해당 주솟값을 가진 Heap 영역에 있는 값을 변경시키는 것이기 때문.<br>
하지만 Han을 또다른 인스턴스로 할당하려는 경우 Stack의 003번 주소에 다른 주솟값을 넣으려는 행위이기 때문에 에러가 난다.<br>

### Cow (Copy on Write)
구조체의 인스턴스를 변수에 복사하면 새로운 인스턴스가 생기지만 COW 메커니즘으로 인해 실제 값을 변경하기 전까지는 복사를 진행하지 않고 값을 참조만 한다.<br>
위에서 설명한 것과 달라 혼란스러울 수 있는데, 구조체에서 값을 복사해서 독립적인 주소 공간을 가지기 전에 한 단계가 더 있다고 보면 된다.<br>
데이터를 참조하는 동안에는 메모리 복사를 최소화해서 효율적으로 메모리를 사용할 수 있도록 한다.<br>


```swift
struct User {
    var name: String
    var age: Int
}
var Kim = User(name: "Kin", age: 17)
var Han = Kim // Kim과 Han은 같은 값을 참조하지만, 아직 값은 복사되기 전.

print(address: Kim) // 0x16fd3b0e0
print(address: Han) // 0x16fd3b0e0
Han.age = 20 // 이때 비로소 Han은 독립적인 주소공간을 가지게 된다.

print(address: Kim) // 0x16fd3b0e0
print(address: Han) // 0x16fd3b160
```
이렇게 Han의 프로퍼티 age의 값이 변경되기 전까지는 Kim과 Han은 같은 값을 가리키지만,
Han.age = 20으로 바꾸는 순간 Han의 값은 복사되어 Kim과 다른 주솟값을 가지게 된다.<br>
<br>

### Mutating
구조체에서 내부 프로퍼티의 값을 변경하기 위해서는 Mutating이라는 키워드가 필요하다.<br>
mutating 없이 구조체의 프로퍼티를 변경하려고 한다면 컴파일 오류가 난다.

```swift
struct User {
    var age: Int
    
    mutating func updateValue(to newValue: Int) {
        age = newValue
    }
}

var a = User(age: 10)
var b = a 

a.updateValue(to: 20)  // a의 값을 변경하려면 `mutating`이 필요
print(b.value)  // 10
```
mutating이라는 키워드로 인해 COW로 인해 인스턴스는 참조만 하고 있다가 프로퍼티가 변경되며 복사가 필요할 수 있다는 것을 컴파일러에게 알려주는 역할을 한다.<br>
반면 mutating 키워드가 없는 함수는 내부 프로퍼티를 읽기 전용으로 사용한다고 보장한다.
<br><br>

# Closure의 Reference Type

```swift
var counter = 0

let increment: () -> Void = {
    counter += 1
}

let anotherIncrement = increment

increment()        // counter = 1
anotherIncrement() // counter = 2
increment()        // counter = 3

counter = 100

increment()        // counter = 101
anotherIncrement() // counter = 102
increment()        // counter = 103
```
increment는 counter를 **캡처**해서 가지고 있고, anotherIncrement 또한 increment와 같은 값을 참조하기 때문에,
counter에 대한 정보를 같이 가지고 있다.<br>
(**캡쳐**한다는 것은 클로저 내부에서 외부에 있는 counter라는 변수를 저장한다는 말.)
따라서 incrementdhk anotherIncrement 모두 counter의 값을 감지하고 변경할 수 있다.<br>

![Image](https://github.com/user-attachments/assets/a5b311f4-8397-4fb4-abc2-13742cb9b92e)
