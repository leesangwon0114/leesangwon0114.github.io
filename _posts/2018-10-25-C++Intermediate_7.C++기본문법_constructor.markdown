---
layout: post
title:  "C++Intermediate&nbsp;7.&nbsp;C++기본문법_constructor"
date:   2018-10-25 04:18:15 +0700
categories: [c++]
---

codenuri 강석민 강사 강의 내용기반으로 정리한 내용입니다.

---

> 상속과 생성자 호출순서

```cpp
#include <iostream>
using namespace std;

class Base
{
public:
    Base() {cout << "B()" << endl;}
    Base(int a) {cout << "B(int)" << endl;}
    ~Base() {cout << "~B()" << endl;}
};
class Derived : public Base
{
public:
    Derived() {cout << "D()" << endl;}
    Derived(int a) {cout << "D(int)" << endl;}
    ~Derived() {cout << "~D()" << endl;}
};

int main()
{
    // Derived d;
    Dervied d(5); // B()가 기반 클래스 생성자가 호출됨
}
```

1. 파생 클래스 생성시 기반 클래스의 생성자가 먼저 호출된다.
2. 기반 클래스 생성자는 항상 디폴트 생성자가 호출된다.
3. 기반 클래스의 디폴트 생성자가 없는 경우 파생 클래스 객체를 만들 수 없다.
4. 기반 클래스의 다른 생성자를 호출하려면 파생 클래스 생성자의 초기화 리스트에서 명시적으로 호출해야 한다.

```cpp
#include <iostream>
using namespace std;

class Base
{
public:
    // Base() {cout << "B()" << endl;}
    Base(int a) {cout << "B(int)" << endl;}
    ~Base() {cout << "~B()" << endl;}
};
class Derived : public Base
{
public:
    Derived() : Base(0) {cout << "D()" << endl;}
    Derived(int a) : Base(a) {cout << "D(int)" << endl;}
    ~Derived() {cout << "~D()" << endl;}
};

int main()
{
    Dervied d(5); // B(int)가 기반 클래스 생성자로 호출됨
}
```

---

컴파일러가 기반클래스 생성자를 넣어주는 것이 위의 동작 원리임

```cpp
#include <iostream>
using namespace std;

class Base
{
public:
    // Base() {cout << "B()" << endl;}
    Base(int a) {cout << "B(int)" << endl;}
    ~Base() {cout << "~B()" << endl;}
};
class Derived : public Base
{
public:
    Derived() : Base() {
        cout << "D()" << endl;
    }
    Derived(int a) : Base() {
        cout << "D(int)" << endl;
    }
    ~Derived() {cout << "~D()" << endl;}
};

int main()
{
    Dervied d(5);
}
```

Base 기반 클래스 default 가 없으면 컴파일 에러가 나는 것임

---

#### protected constructor

자신의 객체를 만들 수 없지만(추상적인 존재), 파생클래스의 객체는 만들 수 있게 한다.

```cpp
#include <iostream>
using namespace std;

class Animal
{
protected:
    Animal() {}
};

class Dog : public Animal
{
public:
    Dog() : Animal() {} // 컴파일러가 default 로 생성
};

int main()
{
    Animal a;   // error
    Dog    d;   // ok..
}
```

---

> 생성자 호출 순서

``` cpp
#include <iostream>
using namespace std;

class Point
{
    int x, y;
publuc:
    Point() : x(0), y(0) {}
    Point(int a, int b) : x(a), y(b) {}
};
class Rect
{
    Point p1;
    point p2;

public:
    Rect() // : p1(), p2() 컴파일러가 만듬
    {}
};

int main()
{
    Rect r; // p1의 생성자, p2의 생성자, Rect 생성자
}
```

Point default 생성자를 주석처리하면 error 발생 -> 사용사가 명시적으로 적어야함

``` cpp
#include <iostream>
using namespace std;

class Point
{
    int x, y;
publuc:
    // Point() : x(0), y(0) {}
    Point(int a, int b) : x(a), y(b) {}
};
class Rect
{
    Point p1;
    point p2;

public:
    Rect() : p1(0, 0), p2(0, 0)
    {}
};

int main()
{
    Rect r; // p1의 생성자, p2의 생성자, Rect 생성자
}
```

---

객체형 맴버함수와 기반클래스 모두 있는 경우

``` cpp
struct BM {BM() { cout << "BM()" << endl;}};
struct DM {DM() { cout << "DM()" << endl;}};

struct Base
{
    BM bm;
    int x;
    Base() { cout << "Base()" << endl; }
};

struct Derived : public Base
{
    DM dm;
    int y;
    Derived() { cout << "Derived()" << endl; }
};

int main()
{
    Derived d;
}
```

메모리에 생성되는 순서대로 생성자가 불림

1. 기반클래스의 멤버의 생성자
2. 기반클래스의 생성자
3. 파생클래스의 멤버의 생성자
4. 파생클래스의 생성자

BM()

Base()

DM()

Derived()

---

> 생성자 호출순서 주의사항

``` cpp
struct stream_buf
{
    stream_buf(size_t sz)
    {
        cout << "stream_buf" << endl;
    }
};

struct stream
{
    stream(stream_buf& buf)
    {
        cout << "stream : using stream_buf" << endl;
    }
};

// 버퍼를 가지고 있는 stream
struct mystream : public stream
{
    stream_buf buf;
public:
    mystream(int sz) : buf(sz), stream(buf) {}
};

int main()
{
    // stream_buf buf(1024);
    // stream st(buf);

    mystream mst(1024);
}
```

코드상으로 멤버데이터의 생성자를 먼저 호출하고 기반클래스를 부르는 것 같지만 생성자 호출은 기반 클래스 stream이 먼저 불려 buf를 초기화 안된 상태로 기반클래스로 전달함

---

``` cpp
struct stream_buf
{
    stream_buf(size_t sz)
    {
        cout << "stream_buf" << endl;
    }
};

struct stream
{
    stream(stream_buf& buf)
    {
        cout << "stream : using stream_buf" << endl;
    }
};

// 버퍼만 관리하는 클래스
struct buf_manager
{
protected:
    stream_buf buf;
public:
    buf_manager(size_t sz) : buf(sz) {}
};

struct mystream : public buf_manager, public stream
{
public:
    mystream(int sz) : buf_manager(sz), stream(buf) {}
};

int main()
{
    mystream mst(1024);
}
```

mystream이 buf_manager 로 부터 buf를 물려받음

기반 클래스가 2개이므로 buf_manager 를 먼저 불리고, buf 를 초기화 시킨 후 stream 초기화 시 buf 부름

---

``` cpp
struct Base
{
    Base() {}
    // void foo() { goo(); }
    virtual void goo() { cout << "Base::goo" << endl; }
};

struct Derived : public Base
{
    int x;

    Derived() : x(10) {}
    virtual void goo() { cout << "Derived::goo" << x << endl; }
};

int main()
{
    Derived d;
    // d.foo();
}
```

Base::goo 가 출력됨

-> 생성자에서는 가상함수가 동작하지 않는다. 기반클래스 호출 시점에 파생클래스 멤버들은 정의되지 않았기 때문에

---

> 생성자와 예외

``` cpp
struct Resource
{
    Resource() { cout << "acquare Resource" << endl; }
    ~Resource() { cout << "release Resource" << endl; }
};

class Test
{
    Resource* p;
publuc:
    Test() : p(new Resource)
    {
        cout << "Test()" << endl;
        // 생성자에서 어떠한 이유로 예외가 발생했다고 가정
        throw 1;
    }
    ~Test()
    {
        delete p;
        cout << "~Test()" << endl;
    }
};

int main()
{
    try
    {
        Test t;
    }
    catch (...)
    {
        cout << "예외 발생" << endl;
    }
}

```

생성자에서 예외가 나면 소멸자를 부르지 못한다.

-> 자원 누수 문제

---

#### 해결책 1. Raw Pointer 대신 스마트 포인터 사용

```
struct Resource
{
    Resource() { cout << "acquare Resource" << endl; }
    ~Resource() { cout << "release Resource" << endl; }
};

class Test
{
    unique_ptr<Resource> p;
publuc:
    Test() : p(new Resource)
    {
        cout << "Test()" << endl;
        throw 1;
    }
    ~Test()
    {
        // delete p;
        cout << "~Test()" << endl;
    }
};

int main()
{
    try
    {
        Test t;
    }
    catch (...)
    {
        cout << "예외 발생" << endl;
    }
}

```

#### 해결책 2. two-phase constructor

``` cpp
struct Resource
{
    Resource() { cout << "acquare Resource" << endl; }
    ~Resource() { cout << "release Resource" << endl; }
};

class Test
{
    Resource* p;
publuc:
    Test() : p(0)
    {
        // 예외 가능성이 있는 어떠한 작업도 하지 않는다.
    }
    // 자원 할당 전용함수
    void Construct()
    {
        p = new Resource;
        // cout << "Test()" << endl;
        throw 1;
    }
    ~Test()
    {
        delete p;
        cout << "~Test()" << endl;
    }
};

int main()
{
    try
    {
        Test t;        // 생성자 100% 생성 보장
        t.Construct(); // 필요한 자원할당
    }
    catch (...)
    {
        cout << "예외 발생" << endl;
    }
}

```

생성자에서 가상함수를 호출이 안되는 것두 호출가능한 구조
