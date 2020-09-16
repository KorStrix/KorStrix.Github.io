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
이런 툴이 만들어 졌습니다. <br>

<br>

### 인스펙터에서 작업 가능한 <br> 커스텀 식트리와 예시 중 인풋 세팅 화면

![](https://github.com/KorStrix/Unity_RelatedElementList/raw/master/ImageForGithub/InputEventSetting.gif?raw=true)
<br>
[깃허브 링크](https://github.com/KorStrix/Unity_RelatedElementList)

위의 결과가 커스텀 인풋 시스템입니다. <br>

<br>

### 유니티 기본 인풋 시스템과 차이점

1. **인풋과 프로그래머가 정의한 커맨드를 비 개발자가 쉽게 작업할 수 있습니다.** <br>

```
기존 방식은 이런식으로 작업하기가 불가능에 가까우며,
해당 항목의 변경사항이 올 경우 비개발자가 빠르게 의도한 대로 작업하여 테스트할 수 있습니다.
```

2. **작업한 내역을 파일로 저장할 수 있습니다.** <br>

```
기존 방식은 한개의 파일(Input Setting.asset)로 관리하기 때문에,
키 세팅 그룹과 키 세팅 그룹간에 비교하기가 힘든 편입니다.
```

3. **저장된 인풋 이벤트 파일을 언제든지 교체할 수 있습니다.** <br>

```
2번에서 파생된 장점으로, 에디터 혹은 런타임 등에
미리 세팅된 인풋 이벤트 파일을 얼마든지 쉽게 변경할 수 있습니다.
```

![](https://github.com/KorStrix/korstrix.github.io/blob/master/_images/Unity%20Input%20System.png?raw=true)

<br>

### 그 외 애매한 장점
1. **인풋과 커맨드를 쉽게 관리할 수 있습니다.** <br>

```
기존의 인풋방식을 따르면 인풋에 관련된 코드가
여기저기 클래스파일에 흩어져 있기 때문에,
인풋이 변경될 경우 수정하기가 번거로운 편입니다.
```

2. **인풋 기록을 쉽게 출력할 수 있습니다.** <br>

```
이를 이용해 사용자가 실행한 명령의 인풋 기록을 Log로 출력하거나,
레이싱게임의 고스트 플레이어 혹은 리플레이 시스템을 쉽게 구현할 수 있습니다.
```

<br>

---
## 깃허브 링크 (완성된 프로젝트)
유니티 무료 에셋인 플래피 버드에 <br>
리플레이 시스템을 탑재한 프로젝트입니다. <br>
https://github.com/KorStrix/Unity_CommandPattern

![](https://github.com/KorStrix/Unity_CommandPattern/blob/master/Image_ForGithub/Replay.gif?raw=true)

---
## 다른 글 보기 (이미지 클릭)
[![](https://github.com/KorStrix/korstrix.github.io/blob/master/_images/Index_Preview.png?raw=true)](https://korstrix.github.io/index/Index/)
