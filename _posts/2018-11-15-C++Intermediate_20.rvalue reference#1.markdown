---
layout: post
title:  "C++Intermediate&nbsp;20.&nbsp;rvalue reference#1"
date:   2018-11-15 04:18:15 +0700
categories: [c++]
---

codenuri 강석민 강사 강의 내용기반으로 정리한 내용입니다.

---

> lvalue vs rvalue

#### lvalue

- 등호(=)의 왼쪽과 오른쪽에 모두 놓일 수 있다.
- 이름(id)를 가진다.
- 단일 문장을 벗어나서 사용될 수 있다.
- 주소 연산자로 주소를 구할 수 있다.
- 참조를 리턴하는 함수

#### rvalue

- 등호(=)의 오른쪽에만 놓일 수 있다.
- 이름(id)이 없다.
- 단일 문장에서만 사용된다.
- 주소 연산자로 주소를 구할 수 없다.
- 값을 리턴하는 함수. 임시객체. 정수/실수형 리터럴

```cpp
#include <iostream>
using namespace std;

int x = 10;
int f1() {return x;}
int& f2() {return x;}

int main()
{
    int v1 = 10, v2 = 10;

    v1 = 20; // v1: lvalue, 20: rvalue
    20 = v1; // error
    v2 = v1;

    int* p1 = &v1; // ok.
    int* p2 = &10; // error.

    f1() = 10; // error.
    f2() = 20; // ok.

    const int c = 10;
    c = 20;

    Point().x = 10; // error
    Point().set(10, 20); // ok. 나중에 temporary proxy에 사용됨(임시객체의 값을 변경)
}
```
c는 rvalue? lvalue? -> lvalue(이름이 있고, 주소도 구할 수 있음), 수정불가능한 lvalue 다.

rvalue는 모두 상수인 것은 아니다.

---

> 연산자와 lvalue

``` cpp
int operator++(int& a, int)
{
    int temp = a;
    a = a + 1;
    return temp;
}
// 전위형 증가 연산자 - 참조리턴
int& operator++(int& a)
{
    a = a + 1;
    return a;
}

int main()
{
    int n = 0;
    n = 10;

    n++ = 20; // operator++(n, int) error.
    ++n = 20; // operator++(n) ok

    ++(++n);

    int a = 10, int b = 0;

    a + b = 20; // error.

    int x[3] = {1,2,3};
    x[0] = 5; // x.operator[](0) = 5; lvalue
}
```

---

``` cpp
int main()
{
    int n = 0;
    int* p = &n;

    decltype(n) n1; // int
    decltype(p) d1; // int*
    decltype(*p) d2; // int ? int & -> *p = 20 int&

    int x[3] = {1,2,3};
    decltype(x[0]) d3; // int&
    auto a1 = x[0]; // int

    decltype(++n) d4; // int&
    decltype(n++) d5; // int
}
```

괄호안에 값이 왼쪽에 올 수 있으면 참조 없으면 값

auto는 참조 속성 없애니까 int 만 남음

---

> rvalue reference

``` cpp
int main()
{
    int n = 10;

    int& r1 = n; //ok
    int& r2 = 10; // error

    const int& r3 = n; //ok
    const int& r4 = 10; // ok

    const Point& r = Point(1, 1);

    규칙 3.
    int&& r5 = n; // error
    int&& r6 = 10; // ok
}
```

일반참조로는 rvalue를 가르킬 수 없지만 const 는 가르킬 수 있음

#### C++ 규칙

- 규칙 1. not const reference 는 lvalue 만 참조 가능
- 규칙 2. const reference 는 lvalue 와 rvalue를 모두 참조 가능
- 규칙 3. rvalue reference 는 rvalue 만 가리킬 수 있다.

const int& r4 = 10; 은 모든 rvalue가 상수인 것은 아니라 문제될 수 있음(Point에는 임시객체에서 상수가 아니였는데 r을 바꾸려고 시도하면 에러 발생)

-> 상수성이 추가되어 원본을 정확히 가르킨 것이 아님

규칙 3이 나오면서 & 하나는 lvalue reference 라 부름

새롭게 나온 && 를 rvalue reference 라 부름

lvalue는 lvalue 만 rvalue는 rvalue 만 가르키지만 const lvalue reference 는 lvalue와 rvalue를 모두 참조 가능하다가 핵심

원본에 상수성이 추가되지 않은 상태로 순수하게 원본을 가르키고 싶은 rvalue가 C++11 문법에 추가됨

move 와 perfect forwarding 만들 때 필요함

---

#### reference 와 함수 오버로딩

``` cpp
#include <iostream>
using namespace std;

void foo(int& a) { cout << "int&" << endl; } // 1
void foo(const int& a) { cout << "const int&" << endl; } // 2
void foo(int&& a) { cout << "int&&" << endl; } // 3

int main()
{
    int n = 10;

    foo(n); // 1번, 1번이 없으면 2번
    foo(10); // 3번, 3번이 없으면 2번

    int& r1 = n;
    foo(r1); // 1번, 없으면 2번

    int&& r2 = 10; // 10은 rvalue, 10을 가르키는 이름있는 r2라는 참조변수는 lvalue 이다.
    foo(r2); // lvalue 이므로 1번 호출됨

    foo(static_cast<int&&>(r2)); // 3
}
```

rvalue를 가르키는 이름이 있는 참조 변수는 lvalue 이다

