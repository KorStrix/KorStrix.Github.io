---
title: "유니티속 C# 작성 시 주의사항 모음"
categories: Unity C# Coding Know
---

## 개요
- 이 글은 **C# 스크립트를 작성할 때 주의 사항** 을 모았습니다.

- **이 글은 컴파일 에러나 작동이 안되는 <br>
  직접적인 사항을 다루지 않습니다.**

- **이 글은 상시 업데이트 될 예정입니다.**


<details>
<summary>Update Log (펼치기)</summary>
<div markdown="1">

---
#### 190618
- 네이버->깃허브 블로그로 글을 옮겼습니다. <br>
- 마크다운을 활용하여 목차 표를 작성하였습니다. <br>
- 항목을 추가했습니다. <br>

#### 190520
- 항목 옆에 (필수), (권장)을 적었습니다. <br>
- 항목을 추가했습니다. <br>

#### 181219
- 최초 작성

---

</div>
</details>

## 목차

| <center>카테고리</center> | <center>제목</center> | <center>수준</center> |
|:---:|:---:|:---:|
| 퍼포먼스 | Class는 new 연산자를 최대한 피하기 | **필수** |
| 퍼포먼스 | 자주 생성되고 파괴되는 오브젝트는 <br> 풀링 기법을 사용 | 권장 |
| 퍼포먼스 | C#에서 string을 수정할 때는 + 연산자가 있지만, <br> StringBuilder 혹은 String.format을 활용 | 권장 |
| 노하우 | 리턴 함수에 null 리턴시 함수 명에 표기 | 권장 |
| 노하우 | 숫자를 담고있는 변수의 변수명에 상수 범위 표시 | 권장 |
| 노하우 | 조건이 있을 땐 최대한 사전에 체크 | 권장 |
| 노하우 | 코드 내에 string을 최대한 피하기 <br> - Enum 및 nameof 사용 | **필수** |
| 퍼포먼스 | Struct의 인스턴스는 ref를 통해 주고받기 | 권장 |
| 퍼포먼스 | List 클래스의 insert 함수는 최대한 피하기. | **필수** |
| 노하우 | 주석은 함수로 대체할 수 있으면 함수로 대체하기. | 권장 |
| 퍼포먼스 | Dictionary의 Key는 Struct일 경우 IEqualityComparer 구현. | 권장 |
| 노하우 | public Field는 되도록 프로퍼티로 작성하기. | 권장 |
| 노하우 | Enum에 Flags Attribute 사용시 <br> Type에 명시하기. | 권장 |

<br>
<br>

## Unity C# 작성 시 주의사항

### Class는 new 연산자를 최대한 피하기 (필수)
Class를 new를 하면 힙에 쌓이며 <br>
결과적으로 가비지가 될 수 있는 코드이므로, 피해야 합니다. <br>

```csharp
public List<int> GetSomthingList_Bad()
{
    List<int> listReturn = new List<int>();

    return listReturn;
}

``
Bad
//========================================
Good

List<int> _listTemp = new List<int>();

public List<int> GetSomthingList_Good()
{
    _listTemp.Clear();

    return _listTemp;
}

```

<br>

### 자주 생성되고 파괴되는 오브젝트는 <br> 풀링 기법을 사용 (권장)
Monobehavior도 Class여서 파괴할 때 가비지를 생성합니다. <br>
또한 유니티 GameObject의 경우Create / Destory 을 처리하는데 <br>
걸리는 시간이 Enable/Disable을 처리시간보다 오래 걸립니다. <br>

<br>

### C#에서 string을 수정할 때는 + 연산자가 있지만, <br> StringBuilder 혹은 String.format을 활용 (권장)
string은 + 연산자를 사용할 때마다 기존 string을 버리고 <br>
새 메모리를 할당하여 string생성 비용 & 가비지를 생성합니다.

```csharp
string Logic_String_Bad()
{
    string strTest = "B";
    strTest += "A";
    strTest += "D";

    return strTest;
}

Bad
//========================================
Bad 2

string Logic_String_Bad2() // 함수 내에 존재하는 Class는 new 연산자를 최대한 피하기
{
    StringBuilder strBuilder = new StringBuilder();
    strBuilder.Append("B").Append("A").Append("D")Append("2");

    return strBuilder.ToString();
}

Bad 2
//========================================
Good

StringBuilder _strBuilder = new StringBuilder();

string Logic_String_Good()
{
    _strBuilder.Length = 0; // String Buiilder 초기화
    _strBuilder.Append("G").Append("O").Append("O")Append("D");

    return strBuilder.ToString();
}
```

<br>

##### 참고 링크
- .NET에서 StringBuilder 클래스 사용 <br>
  https://docs.microsoft.com/ko-kr/dotnet/standard/base-types/stringbuilder

<br>

### 리턴 함수에 null 리턴시 함수 명에 표기 (권장)
사실 null 리턴 시 별도 분기처리가 좋지는 않습니다. <br>
위의 사항이 있을 땐 **디자인 패턴 중 Null Object Pattern으로 <br>
처리하는 방식이 가장 이상적인 방식이나, <br>
실무에서는 항상 이상적으로 처리할만한 여유가 없습니다.** <br>

```csharp
class ClassA
{
}

ClassA SomthingReturn(bool bCreateInstance)
{
    if(bCheck)
        return new ClassA();
    else
        return null;
}

Not Good
//========================================
Good

ClassA SomthingReturn_OrNull(bool bCreateInstance)
{
    if(bCheck)
        return new ClassA();
    else
        return null;
}
```

##### 참고 링크
- Null Object Pattern <br>
  http://sjava.net/2009/09/null-object-pattern/

- [번역] 왜 Null이 나쁜가? <br>
  http://www.mimul.com/pebble/default/2015/11/11/1447231720346.html

<br>

### 숫자를 담고있는 변수의 변수명에 상수 범위 표시 (권장)
멤버 변수 혹은 매개변수에 범위가 정해져 있는 경우가 있습니다. <br>
내부에서 어차피 Clamp처리를 하는 경우라면, <br>
**변수에 표기하여 함수 사용자가 알게끔 하는것이 좋습니다.** <br>

```csharp
float Somthing_Lerp(int iFrom, int iTo, float fProgress)
{
    return Mathf.Lerp(iFrom, iTo, fProgress);
}

Not Good
//========================================
Good

float Somthing_Lerp(int iFrom, int iTo, float fProgress_0_1)
{
    return Mathf.Lerp(iFrom, iTo, fProgress_0_1);
}
```

<br>

### 조건이 있을 땐 최대한 사전에 체크 (권장)
함수 내 괄호문은 유지보수를 위해 최대한 줄이는 것이 좋습니다.

```csharp
public class Player
{
    public int iScore;
    public bool bIsRanker;
    public bool bIsBadUser;
}

void Function_Set_IsRanker(Player pSomePlayer)
{
   if(pSomePlayer.bIsRanker == false && pSomePlayer.bIsBadUser == false)
   {
       if(pSomePlayer.iScore >= 1000)
       {
            pSomePlayer.bIsRanker = true;
       }
   }
}

Not Good
//========================================
Good

void Function_Set_IsRanker(Player pSomePlayer)
{
   if(pSomePlayer.bIsRanker || pSomePlayer.bIsBadUser)
       return;

   if(pSomePlayer.iScore >= 1000)
   {
        pSomePlayer.bIsRanker = true;
   }
}
```

<br>

### 코드 내에 string을 최대한 피하기 <br> - Enum 및 nameof 사용 (필수)
string을 직접 대입하는 것은 **오타로 인해 버그를 유발할 수 있고, <br>
변경에 대한 유지보수에 좋지 않습니다.** <br>
nameof 연산자는 C# 6.0부터 지원하는 기능이며, <br>
Class, Enum 등을 컴파일 과정에서 string으로 변환합니다. <br>

```csharp
void SpawnMonster_GoblinGroup_String()
{
    SpawnMonster("Goblin");
    SpawnMonster("Goblin_Armed");

    Invoke("SpawnMonster_GoblinGroup_String", 1f);
}

Bad
//========================================
Good

public enum MonsterType
{
    Goblin,
    Goblin_Armed
}

void SpawnMonster_GoblinGroup_Enum() // string 버젼 함수와 성능은 같습니다.
{
    SpawnMonster(nameof(MonsterType.Goblin));
    SpawnMonster(nameof(MonsterType.Goblin_Armed));

    Invoke(nameof(SpawnMonster_GoblinGroup_Enum), 1f);
}
```

##### 참고 링크
- nameof(C# 참조) <br>
  https://docs.microsoft.com/ko-kr/dotnet/csharp/language-reference/keywords/nameof

<br>

### Struct의 인스턴스는 ref를 통해 주고받기 - (권장)
Struct는 변수를 정의할 때마다 생성자를 통해 값을 통째로 복사합니다. <br>
이 때 생성자 비용은 struct의 데이터 양만큼입니다. <br>
하지만 **ref를 통해서 주고받을 경우 생성자 비용이 없습니다.** <br>

```csharp
struct TestStruct
{
     public void SomthingWork()
     {
     }
}

TestStruct Struct_Function(TestStruct sStruct) // 매개변수에 있는 struct의 생성자 호출
{
     sStruct.SomthingWork();
     return sStruct; // Return과 동시에 struct 생성자 또 호출
}

Not Good
//========================================
Good

void Struct_Function(ref TestStruct sStruct) // 생성자 호출 x
{
     sStruct.SomthingWork();
}
```

##### 참고 링크
- Pass by value vs Pass by reference performance C#.net
https://stackoverflow.com/questions/3395873/pass-by-value-vs-pass-by-reference-performance-c-netPass

<br>

### List<T> 클래스의 insert 함수는 최대한 피하기. (필수)
C# List의 insert함수는 index에 item을 삽입하는 것입니다. <br>
편한 기능이지만, List는 내부가 Array로 구현되있습니다. <br>
**따라서 insert를 하면 insert 함수의 index의 item을 <br>
모두 한칸씩 뒤로 옮기는 비용이 발생합니다.** <br>

<br>
예) 1000개가 들어있는 List에 insert(0)을 호출할 경우 <br>
1000개의 아이템을 한칸씩 뒤로 옮기는 비용 발생 <br>

##### 참고 링크
- Does List.Insert have any performance penalty?
https://stackoverflow.com/questions/18587267/does-list-insert-have-any-performance-penaltyDoes

<br>

### 주석은 함수로 대체할 수 있으면 <br> 함수로 대체하기. (권장)
주석의 작성방법은 아직도 논쟁이 있지만, <br>
실무가 바쁘다는 가정 하에, 주석이 많고 설계가 변경될 경우 <br>
변경된 사항을 주석에 깜빡하고 업데이트를 못할 수 있습니다. <br>
**함수로 대체할 수 있는 주석은 함수로 작성합시다.** <br>

```csharp
// 예시는 단순한 덧셈 곱셈 예시를 들었으나,
// 덧셈 곱셈의 단위를 실무 속의 기능 하나로 생각하시면 됩니다.
void Something_Complex_Logic()
{
  int a = 1;
  a = a + 1; // 1을 더합니다.
  a = a * 1; // 1을 곱합니다.
}

Not Good
//========================================
Good

void Something_Complex_Logic()
{
  int a = 1;
  a = AddNumber(a, 1);
  a = Multiply_Number(a, 1);
}

int AddNumber(int iNumber_A, int iNumber_B) { return iNumber_A + iNumber_B; }
int Multiply_Number(int iNumber_A, int iNumber_B) { return iNumber_A * iNumber_B; }
```


### Dictionary의 Key가 Struct일 경우 <br> IEqualityComparer<> 구현. (권장)
C# Dictionary의 경우 Ke를 Object.Equals를 통해 비교하는데, <br>
Struct도 Object(참조형)으로 변환하여 Equals를 호출합니다. <br>
**IEqualityComparer<>를 구현하면 Key를 Get/Set 할 때마다 <br>
가비지가 쌓이지 않습니다.** <br>

<br>
예시는 참고 링크의 경우가 더 잘되있으므로 생략하겠습니다. <br>

##### 참고 링크
- GC없이 C# Dictionary에서 enum을 key로 쓰기
  https://libsora.so/posts/csharp-dictionary-enum-key-without-gc/

<br>

### public Field는 되도록 프로퍼티로 작성하기. (권장)
객체는 외부에 최대한 노출하는 것을 자제해야 합니다. <br>
**객체지향적으로 이것을 은닉성이라고 합니다.** <br>

```csharp
public class SomeClass
{
  // public으로 get, set
  public int Value;
}

Not Good
//========================================
Good

public class SomeClass
{
  // public으로는 get, private으로 set
  public int Value { get; private set; }

  // 접근제한은 위와 같지만, 생성과 동시에 2로 초기화.
  public int Value_2 { get; private set; } = 2;
}
```

### Enum에 Flags Attribute 사용시 <br> Type에 명시하기. (권장)
Enum의 Flag Attribute는 Bit Mask를 통해 <br>
한 필드 값에 여러 값을 넣을 수 있는 장점이 있습니다. <br>
하지만 Flag라는 것을 알지 못해 여러값이 들어있는지 <br>
안들어있는지 **사용자 입장에서는 한번에 알기 힘듭니다.** <br>

```csharp
[System.Flag]
enum State
{
  Idle,  Battle,
}

Not Good
//========================================
Good

[System.Flags]
enum StateFlag // 접미어로 Flag를 붙입니다.
{
  Idle,  Battle,
}


---
## 다른 글 보기 (이미지 클릭)
[![](https://github.com/KorStrix/korstrix.github.io/blob/master/_images/Index_Preview.png?raw=true)](https://korstrix.github.io/index/Index/)
