---
title: "유니티 개발자가 본 디자인 패턴"
categories: DesignPattern
---

## 개요
이 글은 유니티 개발자가 디자인 패턴이라는 것을 <br>
어떻게 응용하는 지에 대해 포스팅합니다. <br>

### 디자인 패턴이란?
간략하게 설명하자면 <br>
옛날에 GOF(**G** ang **O** f **F** our = 사천왕)라는 <br>
네명의 천재 소프트웨어 개발자가 <br>
소프트웨어에서 공통적으로 자주 나타나는 구조 및 설계들을 <br>
모아서 **디자인 패턴** 이라고 칭하였습니다.

![](https://github.com/KorStrix/korstrix.github.io/blob/master/_images/GOF.png?raw=true) <br>
**이미지 출처 :  How important are design patterns in software development?.** <br>
https://www.quora.com/How-important-are-design-patterns-in-software-development <br>
(좌측부터 Erich Gamma, Richard Helm, Ralph Johnson and John Vlissides) <br>

~~저는 처음 디자인 패턴을 들었을 때<br>
디자인쪽에서 쓰는 기술 이름인줄 알았습니다.~~ <br>

<br>

보다 자세한 내용은 하단 링크를 참고 <br>

#### 나무위키 - 디자인 패턴
https://namu.wiki/w/%EB%94%94%EC%9E%90%EC%9D%B8%20%ED%8C%A8%ED%84%B4

<br>

### 공부한 책
제가 디자인 패턴에 대해 읽은 책으로 기억에 나는것은 <br>
<br>

![](https://github.com/KorStrix/korstrix.github.io/blob/master/_images/%EC%B1%85_Headfirst%20-%20%EB%94%94%EC%9E%90%EC%9D%B8%ED%8C%A8%ED%84%B4.png?raw=true)

**Head First - Design Pattern**
https://book.naver.com/bookdb/review.nhn?bid=1882446

<br>
<br>

![](https://github.com/KorStrix/korstrix.github.io/blob/master/_images/%EC%B1%85_GoF%EC%9D%98%20%EB%94%94%EC%9E%90%EC%9D%B8%ED%8C%A8%ED%84%B4.png?raw=true)

**GOF의 디자인 패턴**
https://book.naver.com/bookdb/book_detail.nhn?bid=8942623

<br>
<br>

![](https://github.com/KorStrix/korstrix.github.io/blob/master/_images/%EC%B1%85_%EA%B0%9D%EC%B2%B4%EC%A7%80%ED%96%A5%EA%B3%BC%20%EB%94%94%EC%9E%90%EC%9D%B8%ED%8C%A8%ED%84%B4.png?raw=true)

**객체 지향과 디자인 패턴**
https://book.naver.com/bookdb/book_detail.nhn?bid=7255217

이며, 저는 이중 **Head First - Design Pattern** 를 감명깊게 보고 <br>
3~4번정도 더 읽었습니다. **(강력 추천)**

<br>

어찌됐건, 디자인 패턴 관련 책을 읽고 <br>
유니티 개발자로서 어떻게 유니티에 접목시켰는지 <br>
차근차근 적겠습니다.

<br>

### 유니티 개발자로서 디자인 패턴을 공부
디자인 패턴을 들으면 디자인 패턴을 꼭 써야 한다는 <br>
**욕심** 이 생길 수 있습니다. <br>
~~저도 마찬가지였습니다.~~ <br>

하지만 디자인 패턴의 예시는 비 게임분야가 많아서, <br>
쉽게 접목시키기 어려운 부분이 많습니다. <br>

게다가 주변 유니티 개발자 분들은 디자인 패턴에 부정적인 <br>
시각을 가지신 분들이 종종 있기도 하구요. **(호불호)** <br>

보통 부정적인 시각을 가지신 분은 이렇습니다. <br>

1. **적용 시키면 구조가 더 복잡해진다.**

2. **적용 시키면 퍼포먼스가 떨어진다.**

3. **게임은 기획이 바뀌면 설계를 바꿔야 할 때가 있다.** <br>
    **= 어차피 나중에 갈아 엎게 되어있다.** <br>

<br>

이 부정적인 시각은 다 맞는 얘기입니다. <br>

이 글의 좀 밑에 <br>
**유니티에 적용시키니까 효과가 있었나요?** <br>
이에 대한 내용에 대해 적겠습니다.

<br>

### 유니티에서 어떻게 구현하는지?

보통 유니티 개발자분들이 많이 적용하는 디자인 패턴은 <br>
대표적으로 Singleton Pattern이 있습니다. <br>


<details>
<summary>Singleton Pattern이란? (펼치기)</summary>
<div markdown="1">

---

싱글톤은 이름으로 알 수 있듯이 프로그램에 인스턴스가 하나만 <br>
있도록 보장되있는 객체입니다.

<br>
보통 유니티에서는 Manager 객체를 만들 때 사용합니다. <br>

<br>
저 역시 디자인 패턴을 모른 상태에서 싱글톤 패턴을 먼저 배우고 <br>
구현했던 경험이 있네요.

---

</div>
</details>

<br>

저도 처음에 썼듯이, 패턴 책을 따라해서 <br>
억지로 적용한 패턴이 여러개 있었습니다. <br>

**(공부법으로는 프로젝트에 직접 적용하해보는 것을 추천하지만,**  <br>
**이런 작업은 절대 회사 프로젝트에서 적용하면 안됩니다)** <br>

제가 지금까지 종종 쓰는 패턴으로는 <br>
**싱글톤, 옵저버, 상태(FSM), 커맨드** 등이 있네요. <br>

<br>

### 유니티에 적용후 장점

디자인 패턴 공부하면 생기는 장점은 다음과 같습니다.
1. **전문 용어로서의 기능** <br>
Headfirst-Design Patterns에서 나온 예시로, <br>
용어를 아는 다른 이에게 짧고 정확히 이야기할 수 있습니다. <br>

```
전문 용어를 쓰지 않을 경우)

관리자 클래스는 인스턴스가 하나이며,
다른 곳에서 쉽게 접근할 수 있는 구조입니다.

------------------------------------------

전문 용어를 쓴 경우)

관리자 클래스에 싱글톤 패턴을 접목시켰습니다.
```

<br>

2. **검증된 설계 방법** <br>
이미 자타공인의 소프트웨어 전문가(**GOF**)가 분석하고<br>
추려내서 선정하고 세계에 발표한 설계방법이며, <br>
이 내용을 토대로 다른 소프트웨어 전문가들이 <br>
 **다시 연구하며 계속 개량한 학문** 입니다. (**현재진행**) <br>
<br>
이에 대해 공부하면서 꼭 맞는 케이스를 찾기 보다, <br>
더 근본적인 객체지향 설계법을 익히는 것이죠.

<br>

**공통적인 결과로 유지보수를 편하게 해준다는 장점이 있습니다.** <br>
근데 왜 위와 같은 장점이 있는데, 부정적인 시각이 있을까요? <br>

<br>

### 디자인패턴이 왜 호불호가 있는지 고찰

<br>
제 생각을 말하자면, (제 경험에 따른 생각입니다.) <br>
일단 디자인 패턴이라는 것은 굉장히 경험 중심의 분야입니다. <br>

<br>
또한 설계에 직접적으로 연관된 학문인데, <br>
설계를 잘하려면 요구사항을 정확하게 파악해야 합니다. <br>

<br>
그러나 게임은 서비스를 하고, 업데이트를 해나가며 <br>
최초 기획과 많이 변경되어 나가는 경우가 많습니다.<br>

~~(심지어 개발 중에도 많이 바뀔 수 있습니다.)~~

![](https://github.com/KorStrix/korstrix.github.io/blob/master/_images/%EB%B0%94%EB%9E%8C%EC%9D%98%EB%82%98%EB%9D%BC_%EB%AA%A8%EC%95%84%EC%9A%94.png?raw=true)
**이미지 출처 : <br>
[기승전모] '바람의 나라'의 과거와 현재에 대해 알아보자.** <br>
https://www.youtube.com/watch?v=di_1cZn9ztE

<br>
요구사항 및 향후 행보가 어떻게 될지 모르기 때문에, <br>
상대적으로 설계에 대한 중요도가 떨어지고, <br>
이러한 설계기법 중 하나가 디자인 패턴이기때문에, <br>
디자인 패턴을 프로젝트에 맞게 적용시키기가 매우 힘든 편이죠. <br>

<br>

**(위 의견은 국내 온라인/모바일 게임 기준입니다.)**

<br>

### 유니티에서의 사용 예시 (블로그 다른글)

사용 예시는 블로그에 하나씩 포스팅하였습니다.

1. **옵저버 패턴**
https://korstrix.github.io/unity/unitylibrary/designpattern/Observer_Pattern/

2. **커맨드 패턴**
https://korstrix.github.io/unity/unitylibrary/designpattern/Command_Pattern/

---
## 목차 링크
https://korstrix.github.io/index/Index/
