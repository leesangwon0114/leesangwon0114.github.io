---
layout: post
title:  "C++Intermediate&nbsp;11.&nbsp;C++11,14기본문법#1"
date:   2018-11-01 04:18:15 +0700
categories: [c++]
---

codenuri 강석민 강사 강의 내용기반으로 정리한 내용입니다.

---

> using

#### template alias

기존에 typedef 도 있는데 using 이 나온이유

Point 를 다른 이름으로 쓰고 싶을 때 typedef 는 템플릿이라 error 발생

-> using 과 template 이용가능

```cpp
#include <iostream>
#include <cstdlib>
using namespace std;

template<typename T> struct Point
{
    T x, y;
};

// typedef int DWORD;
// typedef void(*FP)(int);
typedef Point Pixel; // ???

using DWORD = int;
using FP = void(*)(int);

template<typename T>
using Pixel = Point<T>;

int main()
{
    DWORD n; // int
    FP p; // void(*p)(int)

    Point<int> p1;
    Pixel<int> p2; // Point<int> p2
}
```

- typedef : 타입에 대해서만 별칭(alias)를 만들 수 있다
- using : 타입뿐 아니라 템플릿에 대한 별칭도 만들 수있다.

#### using 문법기본 모양

using id = type-id;

template<template-parameter-list>
using id = type-id;

---

#### using 을 잘 활용하는 코드 살펴보기

``` cpp
// 1. type alias
using DWORD = int;

// 2. template alias
template<typename T, typename U>
using Duo = pair<T, U>;

Duo<int, double> d1; // pair<int, double>

template<typename T>
using Ptr = T*;
Ptr<int> p2; // int*

// 3. 템플릿의 인자를 고정하는 방식
template<typename T>
using Point = pair<T, T>;
Point<int> p; // pair<int, int>

// 4. 복잡한 표현을 단순하게
template<typename T>
using remove_pointer_t = typename remove_pointer<T>::type t;

template<typename T> void foo(T a)
{
    // T에서 포인터를 제거한 타입을 구하고 싶다.
    // typename remove_pointer<T>::type t; // C++11
    remove_pointer_t<T> t; // 위의 코드와 동일 C++14(이미 되어있음)
}
```

using 문법의 도입은 C++11

> static_assert
