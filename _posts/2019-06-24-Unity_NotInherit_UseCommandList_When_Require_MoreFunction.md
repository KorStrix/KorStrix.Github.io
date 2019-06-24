---
title: "유니티 상속말고 실행자 리스트를 통한 기능확장"
categories: Unity UnityLibrary Github
---

## 개요
이 글은 유니티에서 기능확장을 <br>
상속이라는 방법 외에 실행자 리스트라는 <br>
예시를 통해 기능을 확장하며, 이로 인한 장점을 설명합니다. <br>

<br>

### 기존의 상속을 통한 기능 확장 예시

#### 예시
지상 유닛만 있는 게임에서, <br>
두발로 걷는 유닛과 네발로 걷는 유닛을 <br>
구현해야 한다는 가정입니다. <br>

<br>

#### 구현
유닛 클래스와 걷는 방법의 클래스를 참조하고 <br>
걷는 방법 클래스는 abstrac로 움직이는 방법을 제공하고 <br>
상속받는 Class가 움직이는 방법을 구체화를 합니다. <br>

<br>

```csharp
public class Unit
{
  How_Move pMove;
}

abstract class How_Move
{
  protected abstract void Move();
}

class TwoLeg : How_Move
{
  protected override void Move() { // 두발로 걷는 로직}
}

class FourLeg : How_Move
{
  protected override void Move() { // 네발로 걷는 로직 }
}
```

<br>

이 방식은 전통적인 기능 확장 방식입니다. <br>
앞으로 걷는 방법이 추가될 때마다 <br>
TwoLeg, FourLeg와 같이 클래스를 구현하면 되지요. <br>

<br>

제가 제안하는 실행자 리스트는 이렇습니다.



---
## 다른 글 보기 (이미지 클릭)
[![](https://github.com/KorStrix/korstrix.github.io/blob/master/_images/Index_Preview.png?raw=true)](https://korstrix.github.io/index/Index/)
