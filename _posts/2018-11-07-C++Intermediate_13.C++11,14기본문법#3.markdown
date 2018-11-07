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

``` cpp
class Base
{
public:
    void foo(int a) {}
};

class Dervied : public Base
{
public:
    // using Base::foo;
    void foo() {}
};

int main()
{
    Derived d;
    d.foo(10); // error
    d.foo();
}
```

컴파일 에러 발생

d.foo(10)은 이제 사용할 수 없음

같은 이름의 함수가 Derived 에 존재하면 Base 기반 이름들이 다 사라지는 현상 발생

using Base::foo; 를 사용해주면 해결됨

#### 상수에서 함수 이름 충돌
- 파생 클래스와 동일한 이름을 가지는 함수가 기반 클래스에 있다면, 기반 클래스의 함수는 가려지게 된다.
- 버그를 방지하기 위해 만든 문법
- 사용하려면 using을 사용해야 한다.

---

#### 생성자 상속
- using 을 사용하면 생성자도 상속된다.

``` cpp
#include <iostream>
#include <string>
using namespace std;

class Base
{
    string name;
public:
    Base(string s) : name(s) {}
};

class Dervied : public Base
{
public:
    using Base::Base; // 기반클래스의 생성자를 상속해달라
    // Derived(string s) : Base(s) {}
};

int main()
{
    Derived d("aa");
}
```

---

> override & final

``` cpp
class Base
{
public:
    virtual void f1(int) {}
    virtual void f2() const {}
    virtual void f3() {}
            void f4() {}
};

class Derived : public Base
{
public:
    virtual void f1(double) {} // 실수라 가정
    virtual void f2() {}  // 실수라 가정
    virtual void foo3() {}  // 실수라 가정
    void f4() {} // 같은 함수 또 사용 가상함수가 아닌데...
};

int main()
{

}
```

컴파일러가 에러를 발견하지 못함

-> override 를 사용해 에러 출력 가능

``` cpp
class Base
{
public:
    virtual void f1(int) {}
    virtual void f2() const {}
    virtual void f3() {}
            void f4() {}
};

class Derived : public Base
{
public:
    virtual void f1(double) override {}
    virtual void f2() override {} 
    virtual void foo3() override {}
    void f4() override {}
};

int main()
{

}
```

#### override specifier
- C++11 에서 도입된 문법
- 가상함수 재정의 시에 override를 붙여도 되고 붙이지 않아도 된다.(과거 호환성을 위해)
- 가상 함수 재정의 시 override를 붙이면 보다 안전한 코드를 작서할 수 있다.

---

#### final specifier
- C++11에서 도입
- 가상함수를 재정의 못하게 하거나, 파생 클래스를 만들 수 없게 한다.
- 가상함수 뒤에 붙이는 경우 : 해당 가상함수를 파생클래스에서 더 이상 재정의 할 수 없다
- 클래스 뒤에 붙이는 경우 : 파생 클래스를 마들 수 없다.

``` cpp
#include  <type_traits>
#include <iostream>
using namespace std;

class A
{
public:
    virtual void f1() {}
};

class B final : public A
{
public:
    virtual void f1() override final {}
};
/*
class C : public B
{
public:
    virtual void f1() override {}
};
*/
int main()
{
    bool b = is_final<B>:: value;
    cout << b << endl;
}
```

#### is_final
- C++14에 추가된 traits
- 특정 타입이 final 인지 조사
