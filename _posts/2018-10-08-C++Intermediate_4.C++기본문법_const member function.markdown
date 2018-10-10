---
layout: post
title:  "C++Intermediate&nbsp;4.&nbsp;C++기본문법_const member function"
date:   2018-10-08 04:18:15 +0700
categories: [c++]
---

codenuri 강석민 강사 강의 내용기반으로 정리한 내용입니다.

---

> 상수 멤버 함수의 필요성


```cpp
#include <iostream>
using namespace std;

class Point
{
public:
    int x, y;
    Point(int a = 0, int b = 0) : x(a), y(b) {}

    void set(int a, int b)
    {
        x = a;
        y = b;
    }
    void print() const
    {
        // x = 10; // error. 모든 멤버를상수 취급한다.
        cout << x << ", " << y << endl;
    }

};

int main()
{
    const Point p(1, 1);

    p.x = 10; // error p가 상수니까 안됨
    p.set(10, 20); // error x, y 가 바뀌면 안됨
    p.print(); // ?? 내부적으로 값을 바꾸지 않으니까 가능?
}
```

멤버함수 뒤에 const 를 붙이면 상수 멤버함수 - 안에서 모든 멤버를상수 취급

일반적으로 위의 Point 를 header 와 cpp를 나누어 사용

이때 main에서는 header 만 include 해 안에 내부 내용을 몰라 컴파일러는 p.print() error 냄

호출되게 하려면 print()를 반드시 상수멤버함수가 되어야 함

핵심 : 상수 객체는 상수 멤버함수만 호출할 수 있다.

---

``` cpp
struct Rect
{
    int ox, oy, width, height;

    Rect(int x = 0, int y = 0, int w = 0, int h =0) : ox(x), oy(y), width(w), height(h) {}

    int getArea() { return width * height; }
};

void foo( const Rect& r ) // call by value 보다는 const & 가 좋다.
{
    int n = r.getArea(); // error. - 상수 참조로 받으면 get을 못쓰는 현상...
}

int main()
{
    Rect r(0, 0, 10, 10);

    int n = r.getArea(); // ok.

    foo(r);
}

```

Rect struct 의 get Area() const 붙이면 error 해결됨

상수함수는 선택이 아니라 필수다!

객체의 상태를 변경하지 않은 모든 멤버함수는(get***) 반드시 const 멤버함수가 되어야 한다.

---

> 논리적 상수성

``` cpp
class Point
{
    int x, y;

public:
    Point(int a = 0, int b = 0) : x(a), y(b) {}

    const char* toString() const
    {
        static char s[16];
        sprintf(s, "%d, %d", x, y);
        return s;
    }

};

int main()
{
    Point p1(1, 1);
    cout << p1.toString() << endl;
    cout << p1.toString() << endl;

    cont Point p2(2, 2);
    cout << p2.toString() << endl; // toString 이 x, y 변경안해도 허용안됨 -> toString 뒤에 const 붙여야함
}
```

``` cpp
class Point
{
    int x, y;
    char cache[16];
    bool cache_valid = false;
public:
    Point(int a = 0, int b = 0) : x(a), y(b) {}

    const char* toString() const
    {
        if (cahce_valid == false) {
            sprintf(cache, "%d, %d", x, y);
            cache_valid = true; // 상수함수라 여기서 변경 불가
        }
        return cache;
    }

};

int main()
{
    Point p1(1, 1);
    cout << p1.toString() << endl;
    cout << p1.toString() << endl; // 두번 출력 시 cache 되는 기능 추가
}
```

외부에서 바라봤을 때 상태를 변경하지 않아 toString 이 상수가 되는 것이 맞지만 내부적으로 테크닉을 쓰다보니 값을 변경해야 할 때가 있음

**해결책 1. char cache 앞에 mutable char 로 변경 -> mutable 멤버변수: 상수 멤버함수 안에서도 값을 변경 가능.**

``` cpp
class Point
{
    int x, y;
    mutable char cache[16];
    mutable bool cache_valid = false;
public:
    Point(int a = 0, int b = 0) : x(a), y(b) {}

    const char* toString() const
    {
        if (cahce_valid == false) {
            sprintf(cache, "%d, %d", x, y);
            cache_valid = true; // 상수함수이지만 mutable이라 변경 가능
        }
        return cache;
    }

};

int main()
{
    Point p1(1, 1);
    cout << p1.toString() << endl;
    cout << p1.toString() << endl; // 두번 출력 시 cache 되는 기능 추가
}
```

**해결책 2. x, y는 상수멤버함수에서 변하지 않는 멤버이고 cache, cache_valid는 상수멤버함수에서 변하는 멤버임 -> 분리하면됨**


``` cpp
struct Cache
{
    char cache[16];
    bool cache_valid = false;
}
class Point
{
    int x, y;
    Cache* pCache
public:
    Point(int a = 0, int b = 0) : x(a), y(b) {
        pCache = new Cache;
    }

    const char* toString() const
    {
        if (pCache->cahce_valid == false) {
            sprintf(pCache->cache, "%d, %d", x, y);
            pCache->cache_valid = true; // 상수함수이지만 mutable이라 변경 가능
        }
        return pCache->cache;
    }
    ~point() {delete pCache;}
};

int main()
{
    Point p1(1, 1);
    cout << p1.toString() << endl;
    cout << p1.toString() << endl;
}
```

---

> 상수 멤버 함수 참고 사항

``` cpp
struct Test
{
    void foo() { cout << "foo()" << endl; } // 1
    void foo() const { cout << "foo() const" << endl; } // 2

    void goo() const;
};

void Test::goo() const // 구현에도 반드시 const 붙여야 함
{

}

int main()
{
    Test t1;
    t1.foo(); // 1번, 없으면 2번

    const Test t2;
    t2.foo(); // 2번, 없으면 error
}
```
