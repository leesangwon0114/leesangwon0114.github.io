---
layout: post
title:  "C++Intermediate&nbsp;11.&nbsp;C++11,14기본문법#3"
date:   2018-11-07 04:18:15 +0700
categories: [c++]
---

codenuri 강석민 강사 강의 내용기반으로 정리한 내용입니다.

---

> delegate constructor

```cpp
#include <iostream>
using namespace std;

struct Point
{
    int x, y;
    // Point() : x(0), y(0) {}
    Point()
    {
        // 다른 생성자를 호출할 수 없을까?
        Point(0, 0); // 기존에는 생성자 내에 생성자를 부르는 것은 잘못된 코드 -> 컴파일 시 error 가 없지만 실행 시 쓰레기 값이 들어가 있음
        // 임시객체를 생성하는 표현임!!!
    }
    Point(int a, int b) : x(a), y(b) {}
};

int main()
{
    Point p;
    cout << p.x << endl;
    cout << p.y << endl;
}
```
C++11 부터 초기화 가능

하나의 생성자에서 다른 생성자를 호출하는 문법을 delegate constructor이라함

```cpp
#include <iostream>
using namespace std;

struct Point
{
    int x, y;
    // Point() : x(0), y(0) {}
    Point() : Point(0, 0) // 0, 0 으로 초기화 가능
    {
    }
    Point(int a, int b) : x(a), y(b) {}
};

int main()
{
    Point p;
    cout << p.x << endl;
    cout << p.y << endl;
}
```

#### 위임생성자(delegate constructor)
- C++11에서 도입된 문법
- 하나의 생성자에서 다른생성자를 호출하는 문법
- 임시객체를 생성하는 표현과 생성자를 호출하는 코드를 헷갈리면 안됨
- new(this)Point(0,0); 로 되긴하지만 보기 어려움

---

> inherit constructor
