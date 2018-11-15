---
layout: post
title:  "C++Intermediate&nbsp;19.&nbsp;temporary object#2"
date:   2018-11-15 04:18:15 +0700
categories: [c++]
---

codenuri 강석민 강사 강의 내용기반으로 정리한 내용입니다.

---

> 값 리턴과 참조 리턴

```cpp
#include <iostream>
using namespace std;

Point p; // 전역변수

Point foo() // 값 리턴 : 임시객체가 리턴됨
{
    return p;
}

Point& goo() // 참조 리턴 : 임시객체가 생성되지 않는다.
{
    return p;
}

int main()
{
    // Point ret = foo();
    foo().x = 10; // error

    goo().x = 20; // ok.

    cout << p.x << endl; // 20

    vector<int> v(10, 2);
    v[0] = 10; // v.operator[](0) = 10;
}
```

``` cpp
int x;

int foo() {return x;}
int& goo() {return x;}

int main()
{
    foo() = 10; // error
    goo() = 10; // ok
}
```

---

> 임시객체가 생성되는 다양한 경우

``` cpp
#include <iostream>
using namespace std;

struct Base
{
    int v = 10;
};
struct Derived : public Base
{
    int v = 20;
};

int main()
{
    Derived d;
    cout << d.v << endl; // 20
    cout << d.Base::v << endl; // 10
    cout << (static_cast<Base>(d)).v << endl; // 10 -> Base 타입의 임시객체 10
    cout << (static_cast<Base&>(d)).v << endl; // 10

    (static_cast<Base>(d)).v = 30; // error
    (static_cast<Base&>(d)).v = 30; // ok
}
```

값 캐스팅 : 임시객체 생성

참조 캐스팅 : d를 볼 때 Base로 본다는 의미

---

#### 연산자와 임시객체

``` cpp

// 후위형. 참조리턴 불가(지역변수를 참조리턴할 수 없음)
// 그냥 n 리턴하면 변경된 값이 나옴(이전값을 리턴해야함) => temp 이용
int operator++(int& n, int) // 전위형과 구분하기 위해 int 존재
{
    int temp = n;
    n = n + 1;
    return temp;
}
// 전위형.
int& operator++(int& n)
{
    n = n + 1;
    return n;
}

int main()
{
    int n = 3;

    n++; // operator++(n, int)
    ++n; // operator++(n)

    ++(++n)

    n++ = 10; // error
    ++n = 10; // ok.    
}
```

---

> 임시객체와 멤버함수

``` cpp
#include <iostream>
using namespace std;

class Test
{
public:
    int data;
    void foo() & { cout << "foo&" << endl; } // lvalue 객체에 대해서만 호출.
    void foo() && { cout << "foo&&" << endl; }

    int& goo() & { return data; }
};

int main()
{
    Test t;
    t.foo();
    int& ret = t.goo();

    int& ret2 = Test().goo(); // error

    Test().foo(); // 임시객체이므로 error -> && 추가해 rvalue에 대해 호출
}
```
