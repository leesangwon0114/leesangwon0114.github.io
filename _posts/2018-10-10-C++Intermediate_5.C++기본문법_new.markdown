---
layout: post
title:  "C++Intermediate&nbsp;5.&nbsp;C++기본문법_new"
date:   2018-10-10 04:18:15 +0700
categories: [c++]
---

codenuri 강석민 강사 강의 내용기반으로 정리한 내용입니다.

---

> new vs operator new

```cpp
#include <iostream>
using namespace std;

class Point
{
    int x, y;
public:
    Point() { cout << "Point()" << endl; }
    ~Point() { cout << "~Point()" << endl; }
};

int main()
{
    // malloc : 메모리만 할당
    Point* p1 = (Point*)malloc(sizeof(Point));
    free(p1);

    // new : 메모리 할당 + 생성자 호출
    Point* p2 = new Point;
    delete p2;

    // C++에서 메모리만 할당
    Point* p3 = static_cast<Point*> (operator new(sizeof(Point)));

    // C++에서 메모리만 해지
    operator delete(p3);
}
```

#### new 동작 방식

1. 메모리 할당 - operator new() 함수 사용
2. 생성자 호출
3. 메모리 주소를 해당 타입으로 반환

#### delete 동작 방식

1. 소멸자 호출
2. 메모리 해지 - operator delete() 함수 사용

operator new 는 void*를 리턴하므로 반드시 casting 이 필요함

---

> operator new 를 재정의

```cpp
#include <iostream>
using namespace std;

class Point
{
    int x, y;
public:
    Point() { cout << "Point()" << endl; }
    ~Point() { cout << "~Point()" << endl; }
};

void* operator new(size_t sz)
{
    cout << "my new" << endl;
    return malloc(sz);
}

void* operator new(size_t sz, const char* s, int n)
{
    cout << "my new2" << endl;
    return malloc(sz);
}

void operator delete(void* p) noexcept
{
    cout << "my delete" << endl;
    free(p);
}

int main()
{
    Point* p = new Point; // 1. 메모리 할당 - operator new(sizeof(Point))
    // 2. 생성자 호출
    delete p;

    Point* p2 = new("AA", 2) Point; // operator new(sizeof(Point), "AA", 2)
}
```

---

> Placement new

```cpp
#include <iostream>
using namespace std;

class Point
{
    int x, y;
public:
    Point() { cout << "Point()" << endl; }
    ~Point() { cout << "~Point()" << endl; }
};

int main()
{
    Point* p = new Point;

    // p.Point(); // error.
    p.~Point(); // ok.
}
```

생성자는 명시적으로 호출할 수 없지만 소멸자는 명시적 호출 가능

---

#### 생성자를 명시적으로 부르는 방법

```cpp
#include <iostream>
using namespace std;

class Point
{
    int x, y;
public:
    Point() { cout << "Point()" << endl; }
    ~Point() { cout << "~Point()" << endl; }
};

void* operator new(size_t sz, Point* p)
{
    return p;
}

int main()
{
    Point p;
    // new Point; // 인자가 1개인 operator new() 호출
    new(&p) Point; // 인자가 2개인 operator new() 호출

    p.~Point();
}
```

#### new 동작 방식

1. 메모리 할당 - operator new() 함수 사용
2. 생성자 호출
3. 메모리 주소를 해당 타입으로 반환

new(&p) Point 는 메모리 할당하는 코드가 아닌 기존에 있던 p객체의 생성자를 호출하는 코드

**이렇게 어떤 객체의 생성자를 부르기 위해 만들어진 new를 placement new 라 부름**

```cpp
void* operator new(size_t sz, void* p)
{
    return p;
}
```

위의 코드가 C++ 표준으로 있고 Point 를 void 로 바꾸어 모든 객체에 대해 동작함

new(&p) Point 만 호출해도 생성자만 호출됨

---

앞으로 new 의 시각을 메모리 할당이 아니라 객체를 생성하는 시각으로 봐야함

``` cpp
int main()
{
    // malloc : 메모리만 할당
    Point* p1 = (Point*)malloc(sizeof(Point));

    // new : 객체의 생성( 메모리 할당 + 생성자 호출 )
    Point* p2 = new Point;

    Point* p3 = new Point;
    Point* p4 = new(p1) Point;
}
```

p3는 새로운 메모리에 객체를 생성해 달라

p4는 기존 메모리에 객체를 생성해 달라 -> placement new

---

> 왜 생성자의 명시적 호출이 필요한가?
