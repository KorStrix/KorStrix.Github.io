---
title: "Unity GetComponent Attribute"
categories: Unity, Attribute
---

## 개요
이 글은 GetComponent Attribute이 무엇이고 왜 사용하는지,<br>
그리고 어떻게 제작하는지와 퍼포먼스 비교에 대해 포스팅합니다.

## GetComponent Attribute가 무엇인가요?
### GetComponent?
Unity를 어느정도 다뤄보셨으면 **GetComponent** 를 사용해보셨을 겁니다.
<br>
비슷한 함수로 GetComponent뿐만 아니라, GetComponentInChildren, GetComponentsInChildren,<br>
GetComponentInParents 등이 있으며, 이 포스트에서는 이들을 묶어 **GetComponent류** 라 칭하겠습니다.
<br>
~~굳이 류를 붙인 이유는 없으면 GetComponent 하나만 생각할 수 있기 때문에..~~
<br>
### Attribute?
GetComponent는 들어봤지만, Attribute는 생소하실 수 있습니다.
**Attribute는 C# 문법이며, 속성이라고도 합니다.**
아마 마찬가지로, Unity를 어느정도 다뤄보셨으면 다음과 같은 코드를 보셨을 것입니다.

```csharp
public class SomthingMono : MonoBehaviour
{
    [SerializeField] // 접근제한자가 private지만 인스펙터에 이 변수를 노출합니다.
    private Rigidbody rigidbody;
}
```

여기서 **[SerializeField]** 와 같은 문법을 Attribute라고 합니다.

<br>

##### 다른 Unity Attribute 예시
- **[HideInInspector]** : 접근제한자와 상관없이 인스펙터에 노출하지 않음.
- **[Range(최소 숫자, 최대 숫자)]** : 인스펙터에 해당 범위만 설정할 수 있는 슬라이더를 노출.
- **[Header(문자열)]** : 인스펙터에 해당 변수 위의 위치에 굵은 글씨로 문자열을 노출.

<br>

위의 Attribute는 변수용 Attribute이며, 클래스, 함수전용 혹은 포함시키는 Attribute를 선언할 수 있습니다.

---
## 그래서 GetComponent Attribute를 왜 사용해야 하냐구요?
### 케이스 1
GetComponent Attribute를 작성하면 하단과 같은 일이 여러분의 VS(Visual Studio)에서 가능해집니다.
```csharp
[GetComponent] // 이 한줄로 하단의 주석처리 된 코드의 기능을 합니다.
Rigidbody rigidbody;
// void Awake()
// {
//    rigidbody = GetComponent<Rigidbody>();
// }
```

### 케이스 2
**겨우 이것밖에 안된다구요?** <br>
**다음과 같은 코드는 어떨까요?**
```csharp
[GetComponentInChildren("Head")]
Rigidbody rigidbody_Head;
[GetComponentInChildren("Left_Leg")]
Transform transform_LeftLeg;
[GetComponentInChildren("Right_Leg")]
Transform transform_RightLeg;

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
//        Debug.LogWarning(name + " Head is Null", this);
//    if (transform_LeftLeg == null)
//        Debug.LogWarning(name + " LeftLeg is Null", this);
//    if (transform_RightLeg == null)
//        Debug.LogWarning(name + " RightLeg is Null", this);
//}
```

### 케이스 3
이 글의 기술을 응용하면 다음과 같은 기능도 가능합니다.
```csharp
enum BodyParts // Enum에 검색할 자식 오브젝트의 이름을 추가합니다.
{
    Head,
    Right_Leg,
    Left_Leg,
}

[GetComponentInChildren] // 이 한줄이면 Dictionary에 해당 이름의 Rigidbody가 할당됩니다.
Dictionary<BodyParts, Rigidbody> bodyparts;
```

---
## 구조 및 설계
~~구조 및 설계는 구현하다 보면 항상 틀리기 마련이지만,~~ <br>
제가 기존에 **이미 작성하고 사용하고 있는** GetComponent Attribute를 기준으로 설명하겠습니다.

### 필요 기능
어떤 **(이 포스트에 설명한 GetComponent Attribute가 되었든, 무엇이 되었든.)** Attribute를 사용하더라도,

<br>

**결과적으로 GetCustomAttributes함수를 통해 우리가 제작한 Attribute를 찾아야 합니다.**
우리는 이 함수를 호출하기까지 다소 ~~귀찮은~~ 절차를 밟아야 합니다.

<br>

차근차근 스텝을 밟아봅시다.
1. 먼저 GetComponent Attribute는 멤버 변수용 Attribute입니다.
2. 멤버 변수는 어떠한 클래스 혹은 구조체(줄여서 클래스)가 선언한 변수입니다.
**= 따라서 어떠한 클래스와 멤버 변수가 필요합니다.**

<br>

클래스를 찾고, 멤버 변수를 찾았으면,
1. 찾은 멤버 변수에 어떤 Custom Attribute가 있는지 찾을 수 있습니다.
2. 위에서 우리가 제작한 GetComponent Attribute를 찾을 수 있습니다.
3. 이 Attribute가 있으면 해당 변수에 할당합니다.
4. **따라서, Attribute를 선언할 때 어떤 GetComponent류로 할당할 지 정해야 합니다.**
4. 근데 어떤 **인스턴스** 의 변수에 할당해야 할까요? 클래스만 가지고는 할당이 불가능합니다.
**= Attribute에 어떤 식으로 변수를 할당할지 담아야 합니다. <br>
또한 변수를 할당할 인스턴스를 찾아야 합니다.**

<br>

#### 필요 기능 요약
1. 우리가 필요한 것은 클래스, 멤버변수, 인스턴스입니다.
2. 또한 우리는 유니티 환경이기 때문에 Monobehaviour Life Cycle에 의존해야 합니다.
3. Attribute에는 어떤 방법으로 GetComponent를 할지 담겨있어야 합니다.

#### 구조 및 설계 (제가 작성한 것 기준)

---
## 깃허브 링크 (완성된 프로젝트)
![](https://github.com/KorStrix/Unity_GetComponentAttribute/raw/master/Images_ForGhithub/Preview.gif?raw=true)

예제와 프로파일러, 사용방법 등이 간단하게 적혀있는 깃허브 프로젝트입니다.

https://github.com/KorStrix/Unity_GetComponentAttribute
