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

~~저는 처음 디자인 패턴을 들었을 때<br>
디자인쪽에서 쓰는 기술 이름인줄 알았습니다.~~ <br>

<br>

보다 자세한 내용은 하단 링크를 참고 <br>

<br>

#### 나무위키 - 디자인 패턴
https://namu.wiki/w/%EB%94%94%EC%9E%90%EC%9D%B8%20%ED%8C%A8%ED%84%B4

<br>

### 공부한 책
제가 디자인 패턴에 대해 읽은 책으로 기억에 나는것은 <br>

**Head First - Design Pattern**
https://book.naver.com/bookdb/review.nhn?bid=1882446

**GOF의 디자인 패턴**
https://book.naver.com/bookdb/book_detail.nhn?bid=8942623

**객체 지향과 디자인 패턴**
https://book.naver.com/bookdb/book_detail.nhn?bid=7255217

이며, 저는 이중에 **Head First - Design Pattern** 를 감명깊게 보고 <br>
3~4번정도 더 읽었습니다. **(강력 추천)**

<br>

어찌됐건, 해당 내용의 책을 읽고 <br>
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
시각을 가지신 분들이 종종 있기도 하구요. (호불호) <br>

보통 유니티 개발자분들이 많이 적용하는 디자인 패턴은 <br>
대표적으로 Singleton Pattern이 있습니다. <br>

<details>
<summary>Singleton Pattern?</summary>
<div markdown="1">

싱글톤은 이름에 들어있듯이 프로그램에 인스턴스가 하나만 <br>
있도록 보장되있는 객체입니다.

<br>
보통 유니티에서는 Manager 객체를 만들 때 사용합니다. <br>

<br>
저 역시 디자인 패턴을 모른 상태에서 싱글톤 패턴을 먼저 배우고 <br>
구현했던 경험이 있네요.

</div>
</details>

---
## 목차 링크
https://korstrix.github.io/index/Index/
