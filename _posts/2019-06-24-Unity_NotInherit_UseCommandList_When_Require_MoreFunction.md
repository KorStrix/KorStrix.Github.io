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

![](https://github.com/KorStrix/Unity_ExecuterList/blob/master/ImageForGithub/ExecuterList.gif?raw=true)

<br>
<br>

### 코드는 다음과 같습니다.

```csharp
public class TwoLeg_Walking : Executer_HowMove
{
    [Range(0, 10)]
    public int WalkingSpeed_2Leg;
    public bool bIsError = false;

    public override void Move(MonoBehaviour pCaller)
    {
        Debug.Log(pCaller.name + " 두발로 걷는중.." + " Speed : " + WalkingSpeed_2Leg, pCaller);
    }

    public override void IExecuter_Check_IsInvalid_OnEditor(CObjectBase pScriptOwner, ref bool bIsInvalid_Default_IsFalse, ref string strErrorMessage_Default_Is_Error)
    {
        if (bIsError)
        {
            bIsInvalid_Default_IsFalse = bIsError;
            strErrorMessage_Default_Is_Error = "두발로 걷기 로직에 Error가 체크되었습니다.";
        }
    }
}

public class FourLeg_Walking : Executer_HowMove
{
    [Range(0f, 10f)]
    public float WalkingSpeed_4Leg;

    public override void Move(MonoBehaviour pCaller)
    {
        Debug.Log(pCaller.name + " 네발로 걷는중.." + " Speed : " + WalkingSpeed_4Leg, pCaller);
    }

    public override void IExecuter_Check_IsInvalid_OnEditor(CObjectBase pScriptOwner, ref bool bIsInvalid_Default_IsFalse, ref string strErrorMessage_Default_Is_Error)
    {
        if (WalkingSpeed_4Leg == 0f)
        {
            bIsInvalid_Default_IsFalse = true;
            strErrorMessage_Default_Is_Error = "현재 스피드가 0입니다.";
        }
    }
}
```

<br>
<br>

### 좀더 예시를 얘기하자면..

예시는 걷는 방법에 대한 예시지만 <br>
좀더 구체적인 예시를 얘기하자면, <br>
데미지를 입었을 때 처리라던가, (이펙트 출력, 사운드 출력 등을 구현) <br>
디버그 상황에서만 호출하는 실행자를 추가하고 편하게 디버깅하기 <br>
**(위의 예시와 같이 특정 수치를 로그로 출력하는 실행자)** <br>
등이 있겠습니다. <br>

<br>
사실 이것은 제가 커맨드 패턴을 구현하며 생각한 발상이였고, <br>
실무에서 사용했을 때 비개발자가 작업할 수 있었습니다. <br>

<br>

### 장점
구체적인 장점은 다음과 같습니다. <br>

1. **개발자는 기능만 구현하고, <br>비개발자는 구현된 기능들을 연결하여 컨텐츠를 생산** <br>
```
제 다른 글들을 보시면 아시겠지만,
제 작업의 초점은 어떻게 해야 비개발자가 편하게
자신이 원하는 기능을 만들 수 있는가에 초점을 맞춥니다.
```

2. **기존의 상속을 통한 기능확장에 비해<br> 코드 관리하기가 쉽습니다.** <br>
```
기존의 상속을 통한 구현은 기능이
살짝만 `변경`되어도 코드를 추가해야 합니다.
하지만 실행자 리스트는 변경에 대한 작업이 코드가 아닌
인스펙터로도 할 수 있기 때문에 작업하기 때문에,
변경에 대한 비용이 상대적으로 적습니다.
```

<br>

---
## 깃허브 링크 (완성된 프로젝트)
실행자 리스트를 간단하게 구현한 위의 예시입니다.
https://github.com/KorStrix/Unity_ExecuterList

![](https://github.com/KorStrix/Unity_ExecuterList/blob/master/ImageForGithub/ExecuterList.gif?raw=true)

---
## 다른 글 보기 (이미지 클릭)
[![](https://github.com/KorStrix/korstrix.github.io/blob/master/_images/Index_Preview.png?raw=true)](https://korstrix.github.io/index/Index/)
