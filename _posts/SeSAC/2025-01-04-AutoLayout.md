---
title:  "AutoLayout 이해하기"
excerpt: "iOS"

categories:
  - iOS
tags:
  - iOS
last_modified_at: 2025-01-04T13:40:02-05:00
---

# AutoLayout

### 오토레이아웃의 개념
**Auto Layout** dynamically calculates the size and position of all the views in your view hierarchy, based on constraints placed on those views.<br>
오토레이아웃은두 뷰 간에 제약 조건을 지정해 각 뷰의 크기와 위치를 동적으로 계산하는 것.<br><br>
### 등장 배경
다양한 디바이스가 나오면서 뷰를 다양한 크기의 화면에 맞게 적용해야 하는 필요가 생겼다. 그래서 뷰의 크기와 위치에 최소한의 제약을 걸어 화면의 크기가 바뀌어도 뷰가 제대로 위치하고 적절한 크기를 갖도록 하기 위해서 생겨난 개념이다.
<br><br>

뷰가 그려질 때 왼쪽 상단을 기준으로 그려지기 때문에 화면의 크기가 바뀌면 위치가 변하게 된다!
1. iPhone 16 Pro max에서 정중앙에 위치한 뷰
<img width="321" alt="스크린샷 2025-01-04 오후 3 39 56" src="https://github.com/user-attachments/assets/0aac0497-4161-4c08-b42b-e38da74a137c" />

2. 같은 위치에 두고 iPhone 13 mini로 디바이스를 바꾼 화면
<img width="271" alt="스크린샷 2025-01-04 오후 3 40 36" src="https://github.com/user-attachments/assets/7a6560cf-8856-49e6-9f73-f7f8bc80043a" />

### 본론
<img width="495" alt="스크린샷 2025-01-04 오후 3 46 24" src="https://github.com/user-attachments/assets/7f7f72a6-9219-4da2-b750-7d117da57239" />

오토레이아웃을 위해서는 아래 제약 조건을 지정해주어야 한다.
- 위치
  - Leading: 왼쪽 여백. 뷰가 시작되는 지점.
  - Trailing: 오른쪽 여백. 뷰가 끝나는 지점.
  - Top: 위쪽 여백.
  - Bottom: 아래쪽 여백
- 크기
  - Width: 너비. Leading과 Trailing 사이의 거리.
  - Height: 높이. Top과 Bottom 사이의 거리.

모든 조건을 지정해 주어야 하는 것은 아니다!<br>
컴퓨터가 다른 경우의 수를 생각하지 않고 해당 뷰를 정확히 위치 시키고 크기를 지정해 줄 수 있을 만큼의 최소한 조건을 지정해주면 된다. 딱 하나의 경우의 수만 존재해야 한다는 말!<br>
예를 들어 Top = 30과 Width = 30만 지정해 주었다면, 뷰는 다양한 Height을 가질 수 있고, 왼쪽에 가까이 위치할 수도, 오른쪽에 가까이 위치할 수도 있어서 많은 경우의 수가 존재해 뷰의 위치와 크기가 모호해진다.<br><br>

그렇다고 너무 많은 조건을 지정해 준다면?<br>
각 조건이 충돌되는 상황이 펼쳐질 수 있다. 만약 허용가능한 너비가 100인데 Leading과 Trailing을 30씩 주고 Width를 50을 주었다면, 총 110으로 뷰를 그릴 수 없게 된다.<br><br>

#### Intrinsic Content Size
뷰가 내부의 콘텐츠를 표시하는 데 필요한 크기를 자동으로 계산.<br>
오토레이아웃을 사용할 때 명시적으로 크기를 지정하지 않고 콘텐츠에 따라 자동으로 뷰의 크기가 정해지는 것이다. 예를 들어 UILabel은 텍스트의 길이와 폰트 크기에 따라 크기가 결정된다.<br>
이떄 필요한 것이 뷰의 **Priority**이다.<br>

#### Priority
오토 레이아웃으로 뷰 간의 간격이 정해져있지만 Intrinsic Content Size에 의해 크기가 지정되어 있지 않다면, 여러 뷰 중 어떤 뷰의 크기를 우선시하여 그려야 할 지 모호해진다. 오토레이아웃은 고정된 값이나 우선순위를 기준으로 레이아웃을 계산한다. 이것들이 제대로 지정되어있지 않으면 에러가 난다.<br>

1. Hugging Priority
공간이 남을 때, 뷰를 더 늘리지 않도록 하는 것. <br>
여기 수평으로 정렬된 두 레이블(blue, red)이 leading = 16, trailing = 16, 두 레이블 사이 간격 = 0 으로 지정되어 있다. 두 레이블의 content size가 충분히 작아서 레이아웃 공간이 많이 남는다면, 두 레이블의 너비는 어떻게 설정되어야 할까?<br><br> blue의 Hugging Prioirty가 더 높다면, 자신의 크기를 유지시키려는 힘이 더 크기 때문에, Intrinssic Content Size에 최대한 가깝게 유지되고, 남은 공간을 red가 차지하게 된다. 
<br><br>

2. Compression Resistance Priority
공간이 부족할 때, 뷰를 더 줄이지 않도록 하는 것.<br>
1번과 같은 상황에서 두 레이블의 content size가 너무 커서 둘 중 하나의 너비를 줄여야 할 때 무엇을 우선시 해야할까?<br><br> 여기서 blue의 Compression Resistance Priority가 더 높다면, blue의 크기는 줄지 않고 유지하고 red의 크기를 줄인다. 이때 공간이 너무 부족하면 red의 텍스트가 잘릴수도 있다.
<br><br>
두 Priority 모두 클 수록 자신의 크기를 유지시키려고 한다!