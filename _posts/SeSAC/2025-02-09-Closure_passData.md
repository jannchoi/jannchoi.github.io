---
title:  "ViewController의 클로저를 통한 역값전달과 데이터의 흐름"
excerpt: "iOS"

categories:
  - iOS
tags:
  - iOS
last_modified_at: 2025-02-09T13:40:02-05:00
---

# 클로저를 통한 역값전달

**FirstViewController**
```swift
@objc func buttonTapped() {
        let vc = SecondViewController()
        vc.passData = { data in
            if let data = data {
                self.star = data
                self.label.text = "Selected Star: \(data)"
            } else {
                return self.star
            }
            return nil
        }
        // SecondViewController로 이동
        navigationController?.pushViewController(vc, animated: true)
    }
```
<br><br>

**SecondViewController**
```swift
    @objc func backButtonTapped() {
        passData?(selectedItem)

        // 첫 번째 화면으로 돌아가기
        navigationController?.popViewController(animated: true)
    }
```
<br><br>

**buttonTapped의 모든 코드를 실행 한 이후, 해당 메서드의 스택프레임이 해제되면서 지역변수도 모두 사라지는데 어떻게 SecondViewController에서 passData를 통해 전달받은 데이터는 FirstViewController의 프로퍼티를 업데이트 할 수 있을까?**
<br>

```swift
import UIKit
import SnapKit

final class FirstViewController: UIViewController {
    var star: String?  // 데이터가 저장될 변수
    private let label = UILabel()
    let mainButton = UIButton()

    override func viewDidLoad() {
        super.viewDidLoad()
        ...
    }
    @objc func buttonTapped() {
        let vc = SecondViewController()
        vc.passData = { data in
            if let data = data {
                self.star = data 
                self.label.text = "Selected Star: \(data)"
            } else {
                return self.star 
            }
            return nil
        }
        navigationController?.pushViewController(vc, animated: true)
    }
}
```


```swift
final class SecondViewController: UIViewController {
    var passData: ((String?) -> (String?))? 
    var selectedItem = "selected"
    let backButton = UIButton()

    override func viewDidLoad() {
        super.viewDidLoad()
        ...
    }
    @objc func backButtonTapped() {
        passData?(selectedItem)
        navigationController?.popViewController(animated: true)
    }
}
```
<br><br>

![Image](https://github.com/user-attachments/assets/9ac18ccb-e35b-4646-922c-d9564b8db510)
<br>

`buttonTapped`메서드가 끝나고 stackFrame에서 해제되면서 그 내부의 지역변수인 vc, vc.passData 정보도 사라지게 된다.<br><br>
하지만 vc객체인 SecondViewController는 NavigationStack에 저장되어있어 해제되지 않고 유지되며,<br>
`SecondViewController`는 passData의 주솟값인 F300을 가지고 있고,`vc.passData`에 할당된 클로저는 여전히 힙의 F300주소에 유지되고 있다.<br><br>
또한 `vc.passData`에 클로저를 할당할 때 self(FirstVC)를 캡처하여 FirstVC의 주솟값인 F000을 가지고 있다.<br><br>
따라서 `backButtonTapped`메서드 내부의 passData코드가 실행될때, 힙의 F300주소로 접근, 내부의 self(FirstVC)주소인 F000 내부의 `star`의 값을 변경할 수 있게 된다.<br>
<br>

- 전체 코드: https://github.com/jannchoi/TestForSwift/tree/passdata/TestPassData
