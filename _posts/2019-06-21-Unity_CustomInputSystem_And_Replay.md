---
title: "유니티 커스텀 인풋 시스템과 리플레이"
categories: Unity UnityLibrary DesignPattern Github
---

## 개요
이 글은 **제가 유니티 커스텀 인풋 시스템을 왜 만들었는지** 와 <br>
이를 응용했을 때의 좋은 점, <br>
그리고 어떻게 리플레이를 시스템을 구현 하는지를 설명합니다. <br>

<br>

## 커스텀 인풋 시스템을 만든 계기
PC 기반 프로젝트에서 Joystick과 PC 키보드 마우스 입력을 <br>
지원해야 했는데, 기존 유니티 시스템을 활용하여 구현했을 떄 <br>
키 수정 요청이 들어올 때마다 제가 수정 하기가 귀찮았습니다. <br>

<br>

어떤식으로 작업해야 기획자가 쉽게 키세팅 및 기능 연결 작업을 <br>
할 수 있을지 고민했습니다. <br>

<br>

### 필요 기능
일단 자유롭게 키를 세팅할 수 있어야 했습니다. <br>
또한 키보드의 Shift + 1 이라던가, <br>
조이스틱의 LTrigger + A 같은 다중입력도 지원해야 했고, <br>
몇 초 이상 누른다 등도 지원해야 했습니다. <br>

<br>

또한 이렇게 세팅된 키와  <br>
기능을 쉽게 연결할 수 있어야 했습니다. <br>

<br>
3번정도 갈아 엎은 결과, <br>
이런 툴이 만들어 졌습니다.



<br>

**유니티 개발자가 본 디자인 패턴**
https://korstrix.github.io/designpattern/Design_Pattern/

<br>

커맨드 패턴은 디자인 패턴 중에 나오는 패턴중에 하나로, <br>
어떠한 요청사항을 함수가 아니라 객체화하여 구현하는 것을 말합니다. <br>

<br>

#### 예시 상황
게임속 유닛의 목적지을 마우스로 설정하는 상황에서 <br>
리플레이를 구현할 때 <br>
**(스타크래프트 유닛 이동 명령과 같음)** <br>

<br>

#### 구현 방법 - 비커맨드 패턴
유닛의 위치 및 회전 정보를 매 Frame마다 저장. <br>

<br>

#### 문제점
이렇게 구현할 경우, 기록할 유닛이 많아지면 많아질수록 <br>
데이터가 매우 커집니다. <br>

예를들어, 프레임당 위치와 회전 정보를 저장한다 하였을 경우 <br>
위치는 int x, y(**total 12byte**)값으로 이루어졌고, <br>
회전정보는 float(**4byte**)값으로 이루어졌다는 가정 하에 <br>
유닛 하나당 30프레임 기준으로 1초당 <br>
**16byte * 30 = 480byte를 기록합니다.** <br>

만약 이러한 유닛이 12개가 있을 경우 <br>
(스타크래프트 기준 한 그룹) <br>

**480byte * 12 = 5,760byte 즉 5MB입니다.**

<br>

#### 구현 방법 - 커맨드 패턴
유닛에게 명령할 경우 해당 명령을 시간과 함께 저장. <br>

<br>

#### 차이점
이경우 해당 명령을 객체화 시켜야 하는데 <br>
객체를 간단하게 디자인 하자면, <br>
(다시 강조하지만 스타크래프트 기준) <br>

<br>

유닛 ID(4byte), 명령 ID(2byte), 타겟(4byte) <br>
로 총 10byte로 구성하고, 시간기록(4byte)까지 합친다면 <br>
명령 한번 할 때마다 총 14byte만 기록하면 됩니다. <br>

매 프레임마다 기록할 필요가 없이 유닛 하나당 14byte죠.
똑같이 1초가 지나도 14byte입니다. <br>

**유닛이 12개가 있으면 총 168bte만 있으면 됩니다.** <br>
리플레이를 할 때에는 해당 명령을 다시 실행하면 됩니다. <br>

## 유니티에서 어떻게 구현해야 하는지?
위의 예시 상황을 기준으로 유니티에 구현해 보겠습니다. <br>

<br>

**코드 양이 많아 간단하게 구현하겠습니다. <br>
완성된 프로젝트는 맨 밑에 링크로 올렸습니다.**

예시) 유닛의 위치 명령 후 움직임을 리플레이하는 코드 <br>
<br>

### 구현 방법 - 비커맨드 패턴

```csharp
Queue<Vector3> queuePosition;
bool bIsRecord;
bool bIsReplay;

void Update()
{
  if(bIsRecord)
  {
    queuePosition.Enqueue(transform.position);
  }
  else if(bIsReplay)
  {
    if(queuePosition.Count == 0)
      return;

    transform.position = queuePosition.Dequeue();
  }
}
```

### 구현 방법 - 커맨드 패턴

```csharp
enum ECommandType : short
{
  Move,
}

class Command
{
  int iUnitID;
  ECommandType eCommand;
  Vector3 vecPos;
}

struct CommandExecute
{
  Command pCommandExecuted;
  float fExecuteTime;

  // 생성자 구현은 생략
  public CommandExecute(Command pCommandExecuted, float fExecuteTime);
}

Queue<CommandExecute> queueCommand;
bool bIsRecord;
bool bIsReplay;

void Update()
{
  if(bIsRecord)
  {
    if(ExecuteCommand)
    {
      _queueCommand.Enqueue(new CommandExecute(ExecuteCommand, Time.time));
    }    
  }
  else if(bIsReplay)
  {
    CommandExecute pExecuteCommand = _queueCommand.Dequeue());
    pExecuteCommand.Execute();

    // 다음 커맨드의 실행시간까지 대기
  }
}

```

---
## 깃허브 링크 (완성된 프로젝트)
유니티 무료 에셋인 플래피 버드에 <br>
리플레이 시스템을 탑재한 프로젝트입니다. <br>
https://github.com/KorStrix/Unity_CommandPattern

![](https://github.com/KorStrix/Unity_CommandPattern/blob/master/Image_ForGithub/Replay.gif?raw=true)

---
## 다른 글 보기 (이미지 클릭)
[![](https://github.com/KorStrix/korstrix.github.io/blob/master/_images/Index_Preview.png?raw=true)](https://korstrix.github.io/index/Index/)
