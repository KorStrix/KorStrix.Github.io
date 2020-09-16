---
title: "Unity GetComponent Attribute로 편하게 GetComponent하기"
categories: Unity UnityLibrary Attribute Github
---

## 개요
이 글은 **GetComponent Attribute가 무엇이고 왜 사용하는지,** <br>
어떻게 제작하는지와 퍼포먼스 비교에 대해 포스팅합니다.

## GetComponent Attribute가 <br> 무엇인가요?
### GetComponent?
Unity를 다뤄보셨으면 **GetComponent** 함수를 보셨을 겁니다.<br>
<br>
비슷한 함수로 GetComponent뿐만 아니라, <br>
GetComponentInChildren, GetComponentsInChildren, <br>
GetComponentInParents 등이 있으며, 이 글에선 이를 묶어 **GetComponent류** 라 칭하겠습니다. <br>
<br>
~~`류`를 붙인건, 없으면 GetComponent만 생각할 수 있기 때문에~~
<br>

### Attribute?
GetComponent는 보셨겠지만, Attribute는 생소하실 수 있습니다. <br>
**Attribute는 C# 문법이며, 속성이라고도 합니다.** <br>
코드를 수식하는 문법으로 씁니다. ~~뭔가 형용사 같은 느낌~~ <br>

<br>

**다음은 흔히 볼 수 있는 예시입니다.** <br>

```csharp
public class SomthingMono : MonoBehaviour
{
    // 접근제한자가 private지만 인스펙터에 이 변수를 노출.
    [SerializeField]
    private Rigidbody rigidbody;
}
```

여기서 **[SerializeField]** 와 같은 문법을 Attribute라고 합니다. <br>
<br>

#### 다른 Unity Attribute 예시
- **[HideInInspector]**
  - 접근제한자와 상관없이 인스펙터에 노출하지 않음.
- **[Range(최소 숫자, 최대 숫자)]**
  - 인스펙터에 해당 범위만 설정하는 슬라이더를 노출.
- **[Header(문자열)]**
  - 인스펙터에 변수 위에 굵은 글씨로 문자열을 노출.

<br>
위의 Attribute는 변수용 Attribute이며 <br>
클래스, 함수전용 혹 포함하는 Attribute를 선언할 수 있습니다. <br>

<br>

---
## 그래서 왜<br> GetComponent Attribute를<br> 사용해야 하냐구요?
### 케이스 1
GetComponent Attribute를 작성하면 하단과 같은 일이<br>
여러분의 코드에서 가능해집니다.

```csharp
// 밑의 한줄로 하단의 주석처리 된 코드의 기능을 합니다.
[GetComponent]
Rigidbody rigidbody;
// void Awake()
// {
//    rigidbody = GetComponent<Rigidbody>();
// }
```

### 케이스 2
**겨우 이것밖에 안된다구요?** <br>
**다음과 같은 코드는 어떨까요?** <br>
다음은 좀더 튜닝한 코드입니다. <br>

```csharp
[GetComponentInChildren("Head")]
Rigidbody rigidbody_Head;
[GetComponentInChildren("Left_Leg")]
Transform transform_LeftLeg;
[GetComponentInChildren("Right_Leg")]
Transform transform_RightLeg;

// 위의 코드는 아래와 같은 기능입니다.
//void Awake()
//{
//    // 이름을 일일이 검색해서 찾아야지..
//    Transform[] TransformChildren = GetComponentsInChildren<Transform>();
//    for(int i = 0; i < TransformChildren.Length; i++)
//    {
//        Transform TransformChild = TransformChildren[i];
//        if (TransformChild.name.Equals("Head"))
//            rigidbody_Head = TransformChild.GetComponent<Rigidbody>();
//        else if (TransformChild.name.Equals("Left_Leg"))
//            transform_LeftLeg = TransformChild;
//        else if (TransformChild.name.Equals("Right_Leg"))
//            transform_RightLeg = TransformChild;
//    }

//    // 물론 못찾았을 때 처리도 로그로 띄우자..
//    if (rigidbody_Head == null)
//        Debug.LogWarning("Head is Null");
//    if (transform_LeftLeg == null)
//        Debug.LogWarning("LeftLeg is Null");
//    if (transform_RightLeg == null)
//        Debug.LogWarning("RightLeg is Null");
//}
```

### 케이스 3
이 글의 기술을 응용하면 다음과 같은 기능도 가능합니다.

```csharp
// Enum에 검색할 자식 오브젝트의 이름을 추가합니다.
enum BodyParts
{
    Head,
    Right_Leg,
    Left_Leg,
}

// 밑의 한줄로 Dictionary에 해당 이름의 Rigidbody가 할당.
[GetComponentInChildren]
Dictionary<BodyParts, Rigidbody> bodyparts;
```

---
## 구조 및 설계
~~구조 및 설계는 구현하다 보면 항상 틀리기 마련이지만,~~ <br>
제가 기존에 **이미 작성하고 사용하고 있는** <br>
GetComponent Attribute를 기준으로 설명하겠습니다. <br>

### 필요 기능
어떤 Attribute를 사용하더라도, <br>
**(이 포스트에 설명한 GetComponent Attribute외 다른것도.)** <br>
**결과적으로 GetCustomAttributes 함수를 통해 <br>
 우리가 제작한 Attribute를 찾아야 합니다.** <br>
우리는 이 함수를 호출하려면 ~~귀찮은~~ 절차를 밟아야 합니다. <br>
숙련된 프로그래머분은 **생략을 추천** 합니다. <br>

<details>
<summary><b>(재미없고 귀찮은 절차과정) (펼치기)</b></summary>
<div markdown="1">

<br>

**차근차근 스텝을 밟아봅시다.** <br>
1. GetComponent Attribute는 멤버 변수용 Attribute입니다. <br>
2. 멤버 변수는 어떠한 클래스 혹은 구조체(줄여서 클래스)가<br> 선언한 변수입니다. <br>

**= 따라서 어떠한 클래스와 멤버 변수가 필요합니다.** <br>

<br>

**클래스를 찾고, 멤버 변수를 찾았으면,** <br>
1. 찾은 멤버 변수에 어떤 Custom Attribute가 있는지<br> 찾을 수 있습니다. <br>
2. 위에서 우리가 제작한 GetComponent Attribute를 찾습니다.
3. 이 Attribute가 있으면 해당 변수에 할당합니다.
4. **따라서, Attribute를 선언할 때<br> 어떤 GetComponent류로 할당할 지 정해야 합니다.**
5. 근데 어떤 **인스턴스** 의 변수에 할당해야 할까요?<br> 클래스만 가지고는 할당이 불가능합니다.<br>

**= Attribute에 어떤 식으로 변수를 할당할지 담아야 합니다. <br>
또한 변수를 할당할 인스턴스를 찾아야 합니다.**

<br>
<br>

</div>
</details>

#### 필요 기능 요약
1. 우리가 필요한 것은 클래스, 멤버변수, 인스턴스입니다.
2. 또한 우리는 유니티 환경이기 때문에 Monobehaviour Life Cycle에 의존해야 합니다.
3. Attribute에는 어떤 방법으로 GetComponent를 할지 담겨있어야 합니다.

<br>

#### 구조 및 설계 (제가 작성한 것 기준)
![](https://github.com/KorStrix/korstrix.github.io/blob/master/_images/GetComponent%20Attribute.png?raw=true)

<br>

### 그 외.. 다른 Attribute

저같은 경우는 **ChildRequireComponent Attribute** 를 <br>
별도로 제작하였습니다. <br>
<br>
**비 개발자가 세팅할 때 필요한 것들을** <br>
**작업 중에 인스펙터로 바로 알려주기 위함입니다.** <br>
<br>
~~이게 없으면 잘못된 세팅으로 인해 개발자만 고생~~ <br>

<br>

#### ChildRequireComponent Attribute 적용 예시
![](https://github.com/KorStrix/Unity_GetComponentAttribute/blob/master/Images_ForGhithub/ChildRequireComponent_BeforeOdin.png?raw=true)

본래 네이밍 규칙은 RequireChildComponent지만, <br>
RequireComponent Attribute랑 접두어가 겹쳐서 <br>
사용시 불편하더라구요. (VS 인텔리전스) <br>

<br>
GetComponent Attribute를 인터페이스로 구현한 이유중 하나는 <br>
다른 **새로운 Attribute에서 GetComponent 로직을 <br>
사용하기 쉽게 하기 위해서** 입니다.

<br>
<br>

## 성능 테스트
기능은 좋은데, 비용이 비싸면 또 문제겠지요? <br>
그래서 한번 테스트해봤습니다. <br>
<br>
GetComponent 함수와 transform Property, Attribute의 <br>
성능 비교 코드입니다. <br>

```csharp
string strTestCase = ETestCase.GetComponent_DefulatProperty.ToString();
for(int i = 0; i < iTestCase; i++)
{
   SCManagerProfiler.DoStartTestCase(strTestCase);
   GetComponent_Property = null;
   GetComponent_Property = transform;
   GetComponent_Property.GetType();
   SCManagerProfiler.DoFinishTestCase(strTestCase);
}

strTestCase = ETestCase.GetComponet_Function.ToString();
for (int i = 0; i < iTestCase; i++)
{
   SCManagerProfiler.DoStartTestCase(strTestCase);
   GetComponent_Function = null;
   GetComponent_Function = GetComponent<Transform>();
   GetComponent_Function.GetType();
   SCManagerProfiler.DoFinishTestCase(strTestCase);
}

strTestCase = ETestCase.GetComponetsInChildren_Function.ToString();
for (int i = 0; i < iTestCase; i++)
{
   SCManagerProfiler.DoStartTestCase(strTestCase);
   GetComponentsChildren_Children_Function = null;
   GetComponentsChildren_Children_Function = GetComponentsInChildren<Transform>();
   GetComponentsChildren_Children_Function[0].GetType();
   SCManagerProfiler.DoFinishTestCase(strTestCase);
}

strTestCase = ETestCase.GetComponet_Attribute_Individual.ToString();
for (int i = 0; i < iTestCase; i++)
{
   SCManagerProfiler.DoStartTestCase(strTestCase);
   GetComponent_Attribute = null;
   SCManagerGetComponent.DoUpdateGetComponentAttribute(this, this, pMemberInfo);
   GetComponent_Attribute.GetType();
   SCManagerProfiler.DoFinishTestCase(strTestCase);
}

strTestCase = ETestCase.GetComponet_Attribute_All.ToString();
for (int i = 0; i < iTestCase; i++)
{
   SCManagerProfiler.DoStartTestCase(strTestCase);
   GetComponentsChildren_Children_Attribute = null;
   SCManagerGetComponent.DoUpdateGetComponentAttribute(this);
   GetComponentsChildren_Children_Attribute[0].GetType();
   SCManagerProfiler.DoFinishTestCase(strTestCase);
}

SCManagerProfiler.DoPrintResult_PrintLog_IsError(true);
```

<br>

## 성능 결과
1만번 호출 결과 GetComponent, Property의 경우<br>
밀리 세컨드단위로 떨어질정도로 작지만,<br>
GetComponent Attribute도 1초정도로 짧은 편입니다. <br>

![](https://github.com/KorStrix/Unity_GetComponentAttribute/blob/master/Images_ForGhithub/Profiler.png?raw=true)

<br>
게다가 런타임 중 항상 실행하는 것이 아니라, <br>
보통 Awake에서 한번만 실행하는 편이죠. <br>

<br>
<br>

---
## 깃허브 링크 (완성된 프로젝트)
![](https://github.com/KorStrix/Unity_GetComponentAttribute/raw/master/Images_ForGhithub/Preview.gif?raw=true)

예제와 프로파일러, 사용방법 등이<br>
적혀있는 깃허브 프로젝트입니다.

https://github.com/KorStrix/Unity_GetComponentAttribute

---
## 다른 글 보기 (이미지 클릭)
[![](https://github.com/KorStrix/korstrix.github.io/blob/master/_images/Index_Preview.png?raw=true)](https://korstrix.github.io/index/Index/)
