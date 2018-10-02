---
layout: post
title:  "C++Intermediate&nbsp;1.&nbsp;C++기본문법_this call#1"
date:   2018-10-01 04:18:15 +0700
categories: [c++]
---

codenuri 강석민 강사 강의 내용기반으로 정리한 내용입니다.

---

> 멤버 함수의 호출 원리


```cpp
class Point
{
    int x = 0, y = 0;
public:
    void set(int a, int b)
    {
        x = a;
        y = b;
    }

    static void foo(int a)  // void foo (int a)
    {
        x = a;  // this-> x = a; 번경해야하는데... this가 없어서 error
        // 따라서 static에서는 멤버변수를 사용할 수 없는 문법이 나오는 것임
    }
}

int main()
{
    Point::foo(10); // push 10
                    // call Point::foo
    Point p1;
    Point p2;

    p1.set(10, 20); // 원리를 생각해 봅시다.
}
```

멤버함수는 객체마다(p1, p2) 만들어지는 것이 아니라 하나밖에 없음

**p1.set일 때 어떤 객체의 set 인지 어떻게 알 수 있을까?**

p1.set(10, 20) 을 보면 인자가 2개 밖에 없어보이지만 컴파일 시

- 컴파일러가 set(&p1, 10, 20) 으로 변경해줌
- 따라서 set(Point* const this, int a, int b)
- x = a; -> this->x = a;
- y = b; -> this->y = b;

set(&p1, 10, 20) 을 어셈블리로 나타내면

push 20

push 10

lea ecx, &p1    rcx, &p1 // 객체 주소는 레지스터로

call Point::set

위와 같이 호출되는 과정을 this call이라함

cl this1.cpp /FAs => this1.asm 어셈블리어 만들어짐

![Alt text](/static/img/C++Intermediate/2.1.PNG)

1.멤버함수는 1번째 인자로 객체의 주소(this)가 추가된다.

2.static 멤버함수는 객체의 주소가 추가되지 않는다.

---

> 멤버 함수 포인터

``` cpp
class Dialog
{
public:
    void Close() {}
}

void foo() {}

int main()
{
    void(*f1)() = &foo;
    // void(*f2)() = &Dialog::Close;
    
    void(Dialog::*f3)() = &Dialog::Close; // ok.. 멤버 함수 포인터.

    Dialog dlg;
    // dlg.f3();   // dlg.Close()의 의미.. 하지만.. f3이라는 멤버를 찾게된다. - error

    // dlg.*f3(); // ".*" : pointer to member operator -> error. 연산자 우선순위 문제.

    (dlg.*f3)(); // ok.. dlg.Close(); -> 함수포인터를 사용해 멤버함수 호출
}

```

void(*f2)() = &Dialog::Close; // error. this 가 추가되는 함수.

**핵심 1. 일반 함수 포인터에 멤버함수의 주소를 담을 수 없다.**

**핵심 2. 일반 함수 포인터에 static 멤버함수의 주소를 담을 수 있다.**

f3(); 으로 호출 가능?

-> error. 객체(this)가 없다.

**핵심 3. 멤버 함수 포인터 모양과 사용법. ".*" (p->*f3)()**

---

> Thread class 만들기

``` cpp

#include<iostream>
#include<windows.h>

using namespace std;

DWORD __stdcall foo(void* p)
{
    return 0;
}

int main()
{
    CreateThread(0, 0, 
        foo, // 스레드로 수행할 함수
        (void*)"A", // 스레드 함수로 보낼 인자
        0, 0);
}

```

빌드하는법 VC: cl this3.cpp /nologo /EHsc

위의 코드는 C 기반임. 아래 Class로 점차 바꾸어감

---

``` cpp
#include<iostream>
#include<windows.h>

class Thread
{
public:
    virtual void Main() {}
};

class MyThread : public Thread
{
public:

    void run()
    {
        CreateThread(0, 0, threadMain, 0, 0, 0);
    }

    DWORD __stdcall threadMain(void* p)
    {
        Main(); // 가상함수 호출
        return 0;
    }

    virtual void Main() {cout << "스레드 작업" << endl;}
};

int main()
{
    MyThread t;
    t.run();    // 이 순간 스레드가 생성되어서 가상함수 Main()을 실행하여야 한다.

    getchar();
}

```

위의 코드 컴파일 시 에러 발생

threadMain this 가 없어야 함!

CreateThread 에 넘어가는 함수는 반드시 void* 하나여야하는데 static 이 없으니까 에러발생함

``` cpp
#include<iostream>
#include<windows.h>

class Thread
{
public:
    virtual void Main() {}
};

class MyThread : public Thread
{
public:

    void run()
    {
        CreateThread(0, 0, threadMain, 0, 0, 0);
    }

    // 1. 아래 함수는 반드시 static 함수 이어야 합니다.
    static DWORD __stdcall threadMain(void* p)
    {
        Main(); // 가상함수 호출
        return 0;
    }

    virtual void Main() {cout << "스레드 작업" << endl;}
};

int main()
{
    MyThread t;
    t.run();    // 이 순간 스레드가 생성되어서 가상함수 Main()을 실행하여야 한다.

    getchar();
}

```

위의 코드 역시 다시 에러 발생

g++에서 without object 라고 Main(); 의 가상함수 호출에서 에러발생

threadMain은 static 이라 this 가 없는데 Main은 this 가 있음

Main(); 은 this->Main() => Main(this)로 변해야 한다.

그러나 static 함수는 this 가 없어 객체 없이 부르려고 한다는 에러메시지가 나오고 있는 것!

void run()은 static 이 아니므로 this가 있음

CreateThread는 4번째 파라미터에 인자로 보낼 수 있음 -> this를 보내 threadMain에서 casting 시킴!


``` cpp
#include<iostream>
#include<windows.h>

class Thread
{
public:
    virtual void Main() {}
};

class MyThread : public Thread
{
public:

    void run()
    {
        CreateThread(0, 0, threadMain, (void*)this, 0, 0);
    }

    // 1. 아래 함수는 반드시 static 함수 이어야 합니다.
    // 2. 아래 함수는 this가 없다. 그래서 함수 인자로 this를 전달하는 기술.
    static DWORD __stdcall threadMain(void* p)
    {
        Thread* const self = static_cast<Thread*>(p);
        self->Main(); // Main(self)

        // Main(); // 가상함수 호출
        return 0;
    }

    virtual void Main() {cout << "스레드 작업" << endl;}
};

int main()
{
    MyThread t;
    t.run();    // 이 순간 스레드가 생성되어서 가상함수 Main()을 실행하여야 한다.

    getchar();
}

```

