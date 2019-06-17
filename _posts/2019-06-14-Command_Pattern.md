---
title: "유니티속 커맨드 패턴"
categories: Unity UnityLibrary DesignPattern
---

## 개요
이 글은 커맨드 패턴이 무엇이고,
유니티에서 어떻게 사용하는지에 대해 포스팅합니다.<br>

### 커맨드 패턴
만약에 디자인 패턴을 모르시면 <br>
디자인 패턴은 블로그의 다른 글을 참고 해주시기 바랍니다. <br>

<br>

**유니티 개발자가 본 디자인 패턴**
https://korstrix.github.io/designpattern/Design_Pattern/

<br>

커맨드 패턴은 디자인 패턴 중에 나오는 패턴중에 하나로, <br>
어떠한 요청사항을 객체화하여

<br>

#### 예시 상황
게임속 유닛의 체력이 있고, 체력을 그릴 UI가 있다고 했을때, <br>

<br>

#### 구현 방법 - 비옵저버 패턴
체력을 담당하는 클래스의 코드에 UI 로직을 적기, <br>

<br>

#### 문제점
이렇게 구현할 경우, 체력을 구동하려면 <br>
UI 로직이 반드시 필요하게 됩니다. <br>

UI가 필요 없는 체력 시스템의 경우는 **어떻게 될까요?** <br>
혹은, <br>
어떤 유닛은 체력이 변경될 경우 이펙트가 터진다면 <br>
**어떻게 코드를 작성해야 할까요?**


<br>

#### 구현 방법 - 옵저버 패턴
체력을 담당하는 클래스는 체력이 변경되고 변경될 때만 <br>
필요한 클래스들에게 어떻게 변경되었다고 알려주기

<br>

#### 차이점
옵저버 패턴으로 구현할 경우, 체력을 그릴 UI 클래스가 <br>
체력을 담당하는 클래스에게 **체력이 변경될 때만** <br>
**어떤 함수를 호출해** 달라 하면 됩니다.

체력을 그릴 필요가 없으면 UI 클래스는 연결하지 않으면 되구요. <br>
<br>
어떤 유닛만 체력이 변경될 경우 이펙트가 터진다면, <br>
이펙트가 터지는 해당 유닛의 체력을 담당하는 클래스에게 <br>
**마찬가지로 체력이 변경될 때만** <br>
어떤 함수를 호출해 달라고 하면 됩니다.

이렇게 구현할 경우 객체지향 적으로 2가지 이점이 있습니다.
SRP(단일 책임 원칙)과 OCP(개방 폐쇄 원칙)인데요.
각각을 간단하게 설명하자면,

<br>

- **SRP (Single Responsibility Principle - 단일 책임 원칙)** <br>
객체는 책임 한가지만 가지면 좋다.

이 원칙은 쉽습니다. <br>
체력을 관리하는 객체는 체력 관련 기능을 책임지고, <br>
이펙트를 출력하는 객체는 이펙트를 출력하는 것만 책임진다.

- **OCP (Open-Closed Principle - 개방-폐쇄 원칙)** <br>
객체는 확장에는 열려있어야 하고 변경은 닫혀있어야 한다.

이 원칙이 살짝 어려울 수 있습니다. <br>
단어가 생략되었기 때문인데요. <br>
여기서 말하는 확장과 변경은 기능입니다. <br>

다시 말해 <br>
**객체는 기능 확장에는 열려있어야 하고,<br>
기능 변경에는 닫혀 있어야 한다.**

그럼 **기능 확장과 기능 변경의 차이** 는 무엇일까요? <br>
**쉽게 설명하자면, 어떤 기능을 추가할 때 코드 수정의 유무입니다.**

<br>
<br>

## 유니티에서 어떻게 구현해야 하는지?
위의 예시 상황을 기준으로 유니티에 구현해 보겠습니다.

예시) 체력을 담당하는 클래스의 코드. <br>
**기능 1.** 체력 변경 로직 담당 <br>
**기능 2.** 변경된 체력 UI에 출력 <br>
**기능 3.** 데미지를 입었거나 죽었을 때 그에 맞는 이펙트 출력.

<br>

### 구현 방법 - 비옵저버 패턴

```csharp
public class ObserverSubject_Example_Legacy : MonoBehaviour
{
    public Image p_Image_HP;

    public int p_iHP_Init = 10;

    [SerializeField]
    private int _iHP_Max;
    [SerializeField]
    private int _iHP_Current;

    public void Damaged(int iDamageAmount)
    {
        _iHP_Current -= iDamageAmount;
        if(_iHP_Current <= 0)
        {
            _iHP_Current = 0;
            Debug.Log(name + " Play On Dead Effect - Legacy");
        }
        else
        {
            Debug.Log(name + " Play On Damage Effect - Legacy");
        }

        // float / 를 해야 결과값이 float이 됩니다.
        // float을 안쓸 경우 결과값이 int가 되어 0을 리턴.
        OnChange_HP(_iHP_Current / (float)_iHP_Max);
    }

    private void Awake()
    {
        _iHP_Max = p_iHP_Init;
    }

    private void OnEnable()
    {
        _iHP_Current = _iHP_Max;
        OnChange_HP(_iHP_Current / (float)_iHP_Max);
    }

    private void OnChange_HP(float fHeath_0_1)
    {
        p_Image_HP.fillAmount = fHeath_0_1;
    }
}
```

<br>
<br>

### 구현 방법 - 옵저버 패턴 사용
```csharp
public class ObserverSubject_Example_UseObserverPattern : MonoBehaviour
{
    public struct OnChangeHP_Arg
    {
        public ObserverSubject_Example_UseObserverPattern pMessenger {get; private set;}
        public float fCurrentHP_0_1 { get; private set; }
        public bool bIsDamaged { get; private set; }
        public bool bIsDead { get; private set; }

        public OnChangeHP_Arg(ObserverSubject_Example_UseObserverPattern pMessenger, bool bIsDamaged, float fCurrentHP_0_1)
        {
            this.pMessenger = pMessenger;
            this.fCurrentHP_0_1 = fCurrentHP_0_1;
            this.bIsDamaged = bIsDamaged;
            this.bIsDead = fCurrentHP_0_1 == 0f;
        }
    }

    public CObserverSubject<OnChangeHP_Arg> p_Event_OnChangeHP { get; private set; } = new CObserverSubject<OnChangeHP_Arg>();

    public int p_iHP_Init = 10;

    [SerializeField]
    private int _iHP_Max;
    [SerializeField]
    private int _iHP_Current;

    public void Damaged(int iDamageAmount)
    {
        _iHP_Current -= iDamageAmount;
        if (_iHP_Current <= 0)
            _iHP_Current = 0;

        p_Event_OnChangeHP.DoNotify(new OnChangeHP_Arg(this, true, _iHP_Current / (float)_iHP_Max));
    }

    private void Awake()
    {
        _iHP_Max = p_iHP_Init;
    }

    private void OnEnable()
    {
        _iHP_Current = _iHP_Max;
        p_Event_OnChangeHP.DoNotify(new OnChangeHP_Arg(this, false, _iHP_Current / (float)_iHP_Max));
    }
}
```

<br>
<br>

### UI를 담당하는 객체의 코드
```csharp
public class ObserverSubject_Example_UI : MonoBehaviour
{
    public Image p_Image_HP;

    private void Awake()
    {
        GetComponent<ObserverSubject_Example_UseObserverPattern>().p_Event_OnChangeHP.Subscribe += OnChange_HP;
    }

    private void OnChange_HP(ObserverSubject_Example_UseObserverPattern.OnChangeHP_Arg pArg)
    {
        p_Image_HP.fillAmount = pArg.fCurrentHP_0_1;
    }
}
```

<br>

### 이펙트 출력을 담당하는 객체의 코드
```csharp
public class ObserverSubject_Example_PlayEffect : MonoBehaviour
{
    private void Awake()
    {
        GetComponent<ObserverSubject_Example_UseObserverPattern>().p_Event_OnChangeHP.Subscribe += OnChange_HP;
    }

    private void OnChange_HP(ObserverSubject_Example_UseObserverPattern.OnChangeHP_Arg pArg)
    {
        if (pArg.bIsDamaged == false)
            return;

        if (pArg.bIsDead)
            Debug.Log(pArg.pMessenger.name + " Play On Dead Effect - Use Observer");
        else
            Debug.Log(pArg.pMessenger.name + " Play On Damage Effect - Use Observer");
    }
}
```


---
## 깃허브 링크 (완성된 프로젝트)
위의 예시가 구현된 깃허브 프로젝트입니다.

https://github.com/KorStrix/Unity_ObserverPattern

---
## 목차 링크
https://korstrix.github.io/index/Index/
