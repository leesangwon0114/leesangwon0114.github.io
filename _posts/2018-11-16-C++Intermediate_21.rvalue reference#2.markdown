---
layout: post
title:  "C++Intermediate&nbsp;21.&nbsp;rvalue reference#2"
date:   2018-11-16 04:18:15 +0700
categories: [c++]
---

codenuri 강석민 강사 강의 내용기반으로 정리한 내용입니다.

---

> reference collapse

int& & r3와 같이 & 가 붙으면 어떻게 될 것인지 reference collapse라함

#### reference collapse 규칙

![Alt text](/static/img/C++Intermediate/21.1.PNG)

&&일 경우만 &&로 붙음

``` cpp
using LREF = int&;  // typedef int& LREF;
using RREF = int&&; // typedef int&& RREF;

int main()
{
    int n = 10;

    LREF r1 = n;
    RREF r2 = 10;
    
    LREF& r3 = n; // int& & r3 = ?
    RREF& r4 = n; // int&& & -> int&
    
    LREF&& r5 = n; // int& && -> int&
    RREF&& r6 = 10; // int&& && -> int&&

    // int& && r7; // 컴파일 에러..
}
```

---

> forwarding reference

``` cpp
void f1(int& a) {} // lvalue 만 인자로 전달 가능. fl(n) : ok, f1(10) : error
void f2(int&& a) {} // rvalue 만 인자로 전달 가능. f2(n) : error, f2(10) : ok

template<typename T> void f3(T& a) {} // T: int&, T& : int& &

int main()
{
    int n = 10;

    // T의 타입을 사용자가 지정할 경우
    f3<int>(n); // f3(int & a) => lvalue 전달 가능.
    f3<int&>(n); // f3(int& & a) => f3(int & a) => lvalue 전달 가능.
    f3<int&&>(); // f3(int && & a) => f3(int & a) => lvalue 전달 가능.

    // 사용자가 T 타입을 지정하지 않은 경우
    f3(10); // error, T를 무엇으로 결정하던지 lvalue라 error 발생
    f3(n); // T : int. ok.
}
```

template 버전은 모든 타입의 lvalue 만 전달 가능.

T를 어떤 형식으로 바꾸어 내든지 결국은 lvalue 가 된다.

---

``` cpp
// T&& : lvalue 와 rvalue를 모두 전달 가능
//       lvalue를 전달하면 T는 lvalue reference 로 결정, 
//       rvalue를 전달하면 T는 값 타입으로 결정.. 전체적인 인자는 rvalue reference 로 결정됨
template<typename T> void f4(T&& a) {} // forwarding reference

int main()
{
    int n = 10;

    // 사용자가 T의 타입을 명시적으로 전달할 때
    f4<int>(10); // f4(int&& a) => rvalue 를 전달.
    f4<int&>(n); // f4(int& && a) => f4(int& a) => lvalue를 전달.
    f4<int&&>(10); // f4(int&& && a) => f4(int&& a) => rvalue를 전달.

    // T의 타입을 명시적으로 전달하지 않을 때
    f4(n); // ok. 컴파일러가 T를 int& 로 결정.
    f4(10); // ok. 컴파일러가 T를 int 로 결정. f4(T&&) => f4(int &&)
}
```

---

``` cpp
void f1(int& a) {}
void f2(int&& a) {}
template<typename T> void f3(T& a) {} // T: int&, T& : int& &
template<typename T> void f4(T&& a) {} // forwarding reference
```

f1 처럼 int& 로 받으면 int 형의 lvalue 전달 가능.

f2 처럼 int&& 로 받으면 int 형의 rvalue 전달 가능.

f3 처럼 T& 로 받으면 모든 타입의 lvalue 버전의 함수 생성(lvalue 만 전달 가능)

f4 처럼 T&& 로 받으면 모든 타입의 lvalue와 rvalue 모든 버전의 함수 생성(lvalue, rvalue 모두 전달 가능) -> 한때는 universal reference 로 불리다 요새 forwarding reference 로 불림

-> 혹시 lvalue를 전달하면 foo(n) => T : int& 로 결정되고 T&& : int& && => int&

-> 혹시 rvalue를 전달하면 foo(10) => T : int 로 결정되고 T&& : int&&

---

#### forwarding reference 주의 사항

``` cpp
template <typename T> void foo(T&& a) {}

template <typename T> class Test
{
public:
    void goo(T&& a) {} // forwarding reference 아님

    template<typename U> void hoo(U&& a) {} // forwarding reference 맞음
};

int main()
{
    int n = 10;

    foo(n); // ok
    foo(10); // ok

    Test<int> t1; // T가 int로 결정. void goo(int&& a)
    t1.goo(n); // error
    t1.goo(10); // ok

    Test<int&> t2; // T가 int&로 결정. void goo(int& && a) => void goo(int&)
    t2.goo(n); // ok
    t2.goo(10); // error
}
```

goo 멤버함수는 forwarding reference 가 아니고 클래스 객체가 생성될 때 T 가 결정됨

forwarding reference 을 위해서는 다시 템플릿을 만들어야함(hoo)
