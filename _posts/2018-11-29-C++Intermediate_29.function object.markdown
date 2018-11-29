---
layout: post
title:  "C++Intermediate&nbsp;29.&nbsp;function object"
date:   2018-11-29 02:18:15 +0700
categories: [c++]
---

codenuri 강석민 강사 강의 내용기반으로 정리한 내용입니다.

---

> Functor

``` cpp
#include <iostream>
using namespace std;

// Function Object(functor)
class Plus
{
public:
    int operator()(int a, int b)
    {
         return a + b;
    }
};

template <typnename T> struct Plus 
{
    T operator()(T a, T b) const
    {
         return a + b;
    }
};

void foo(const Plus& p)
{
     int n = p(1, 2);
}

int main()
{
    Plus p; // plus 타입의 객체

    int n = p(1, 2); // p.operator()(1,2)

    cout << n << endl;
}
```

a + b : a.operator+(b)

a - b : a.operator-(b)

a(); : a.operator()() - 앞에 괄호는 연산자 괄호, 뒤의 괄호는 호출 괄호

a(1, 2); : a.operator()(1,2)

함수객체를 간단히 만들 때는 클래스 보다 구조체 이용

인자로 넘길 때 상수 참조를 사용할 수 있게 const 를 붙임

---

> Functor 장점
