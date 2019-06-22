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


---
작성중


---
## 깃허브 링크 (완성된 프로젝트)
유니티 무료 에셋인 플래피 버드에 <br>
리플레이 시스템을 탑재한 프로젝트입니다. <br>
https://github.com/KorStrix/Unity_CommandPattern

![](https://github.com/KorStrix/Unity_CommandPattern/blob/master/Image_ForGithub/Replay.gif?raw=true)

---
## 다른 글 보기 (이미지 클릭)
[![](https://github.com/KorStrix/korstrix.github.io/blob/master/_images/Index_Preview.png?raw=true)](https://korstrix.github.io/index/Index/)
