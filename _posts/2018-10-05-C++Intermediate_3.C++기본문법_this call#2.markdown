---
layout: post
title:  "C++Intermediate&nbsp;3.&nbsp;C++기본문법_this call#2"
date:   2018-10-05 04:18:15 +0700
categories: [c++]
---

codenuri 강석민 강사 강의 내용기반으로 정리한 내용입니다.

---

> this in inheritance


```cpp
class A {int a;};
class B {int b;};

class C : public A, public B
{
    int c;
};

int main()
{
    C obj;

    A* pA = &obj;
    B* pB = &obj;

    cout << &obj << endl;   // 1000
    cout << pA << endl;     // 1000
    cout << pB << endl;     // 1004
}
```

obj 가 1000번지라 하면, 두번째 기반 클래스의 주소 B는 &obj + sizeof(A) 만큼 이동하게 되어 있음

---

``` cpp
class A {
    int a;
public:
    void fa() { cout << this << endl; }
};

class B {
    int b;
public:
    void fb() { cout << this << endl; }
};

class C : public A, public B
{
    int c;
};

int main()
{
    C obj;

    A* pA = &obj;
    B* pB = &obj;

    cout << &obj << endl;   // 1000
    obj.fa();   // fa(&obj) cout << this => 1000
    obj.fb();   // fb(&obj+sizeof(A)) cout << this => 1004

    void (C::*f)(); // 멤버 함수 포인터

    f = &C::fa;

    (obj.*f)(); // 1000

    f = &C::fb;

    (obj.*f)(); // 1004
}
```

if 에 따라 f = &C::fa;, f = &C::fb 인데 fa, fb인지에 따라 f(&obj), f(&obj+offset) 일 것임

컴파일러가 어떻게 이 것을 알 수 있을까?

다중 상속의 멤버 함수 포인터

void (C::*f)() 를 만들면 메모리에 4byte + this offset 을 붙여서 생성

cout << sizeof(f) << endl; // 32bit 환경에서 8

따라서

f = &C::fa; // f = {fa 주소, 0};
f = &C::fb; // f = {fb 주소, sizeof(A)};

(obj.*f)(); // f.함수주소(&obj + f.this offset)

---

> 멤버 변수 포인터

``` cpp
class Point
{
public:
    int x, y;
};

int main()
{
    int n = 10;
    int* p1 = &n;
    // void(Point::*f)() = &Point::print; // 멤버 함수 포인터
    int Point::*p2 = &Point::x; // 멤버 변수 포인터
    // 0
    int Point::*p3 = &Point::y; // 멤버 변수 포인터
    // 4

    // cout << p3 << endl; -> 결과1 cout 이 멤버 변수 포인터 잘못출력
    printf("%d, %d\n", p2, p3); // 0, 4

    Point pt;
    pt.y = 10;
    pt.*p3 = 20;

    cout << pt.y << endl; // 20
}

```

p2는 진짜 메모리 주소가 아니라 x 라는 변수가 포인터 안에서 얼마만큼 떨어져 있는지 offset 이 들어있는 것임(Point 객체가 현재 없음)

C의 offset_of 기술을 C++에서는 위와같이 할 수 있음
