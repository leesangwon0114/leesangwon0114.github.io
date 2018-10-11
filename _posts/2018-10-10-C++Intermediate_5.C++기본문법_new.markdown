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

#### 메모리 할당과 생성을 분리하면 유연성이 높아짐

``` cpp
#include <iostream>
using namespace std;

class Point
{
    int x;
    int y;
public:
    Point(int a, int b) : x(a), y(b)
    {
        cout << "Point(int int)" << endl;
    }
};

int main()
{
    // Point 객체를 힙에 한개 만들고 싶다.
    Point* p1 = new Point(0, 0); // ok.

    // Point 객체를 힙에 10개 만들고 싶다.
    // Point* p2 = new Point[10]; // error.(default 생성자 없으므로)

    // 1. 메모리만 먼저 힙에 할당
    Point* p3 = static_cast<Point*>(operator new(sizeof(Point)*10));

    // 2. 할당한 메모리에 객체를 생성(생성자 호출)
    for (int i = 0; i < 10; i++)
    {
        new(&p3[i]) Point(0, 0);
    }

    // 3. 소멸자 호출
    for (int i = 9; i >= 0; i--)
    {
        operator delete(p3);
    }

    // 4. 메모리 해지.
    operator delete(p2);

    vector<Point> v(10, Point(0, 0)); // 10개의 메모리를 먼저 잡고, Point(0,0)을 받아서 복사 생성자 10번 부름
}
```

#### vector 메모리 관리

```cpp
#include <iostream>
#include <vector>
using namespace std;

int main()
{
    vector<int> v(10, 0);

    v.resize(7); // 사실 메모리 사용량은 여전히 10임, size 값만 7로 변경

    cout << v.size() << endl; // 7
    cout << v.capacity() << endl; // 10

    // DBConnect : 생성자에서 DB 접속
    vector<DBConnect> v2(10);

    v2.resize(7); // 메모리는 제거하지 않지만 줄어든 객체의 소멸자는 호출해야 한다.(줄어든 3개에 대해 DB Connection을 끊어야함)

    v2.resize(8); // 새로운 객체에 대한 메모리는 있다, 하지만 생성자를 호출해서 다시 DB 접속을 해야 한다.
}
```

생성자하고 소멸자만 명시적으로 호출하여 효율화

#### 또다른 생성자 명시적 호출이 필요한 경우

IPC 공유 메모리를 사용한 프로세스간 통신

HANDLE hMap = CreateFileMappingA(INVALID_HANDLE_VALUE, 0, PAGE_READWRITE, 0, sizeof(Point), 0);

Point* p = (Point*)MapViewOfFile(hMap, FILE_MAP_ALL_ACCESS, 0, 0, 0);

new(p) Point(0, 0)

시스템 콜은 메모리만 할당되어 있고, 생성을 위해 new 를 사용
