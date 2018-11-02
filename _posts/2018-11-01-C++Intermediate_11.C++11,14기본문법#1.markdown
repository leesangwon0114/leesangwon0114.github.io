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

나이가 음수면 문제생길 여지있음 -> assert 이용

#### assert
- cassert 헤더 필요
- assert(expression of scalar type)
- 실행 시간에 expr이 거짓이면 메시지를 출력하고 프로그램을 종료(abort)

``` cpp
#include <iostream>
#include <cassert>
using namespace std;

void foo(int age)
{
    assert(age > 0);
}
int main()
{
    foo(-10);
}
```

---

#### static_assert
- C++11에 도입된 문법
- 실행시간이 아닌 컴파일시간에 주어진 식을 조사해서 거짓이면 진단 메시지를 출력하고 컴파일ㅇ르 멈춤
- 컴파일 시간에 조사 가능한 상수 표현식만 사용가능, 변수는 사용할 수 없다.

``` cpp
#include <iostream>
#include <cassert>
using namespace std;

void foo(int age)
{
    assert(age > 0);
    // static_assert(age > 0, "error"); -> 변수는 컴파일 시간에 몰라 error 발생
}
int main()
{
    static_assert(sizeof(void*) >= 8, "error. 32bit");
    static_assert(sizeof(void*) >= 16); // C++17 -> error 메시지 생략가능

    foo(-10);
}
```

---

#### static assert 활용
- 함수 안에 만들 수 있고, 함수 밖에 만들 수 도 있다.
- 함수 또는 클래스 템플릿을 만들 때 type_traits 를 사용해서 T가 가져야 하는 조건을 static_assert로 조사하는 코드가 널리 사용됨
- static_assert 를 잘 활용하면 가독성 높은 에러 메시지를 만들 수 있다.

``` cpp
#include <iostream>
#include <mutex>
using namespace std;

#pragma pack(1)
struct PACKET
{
    char cmd;
    int data;
};

static_assert(
    sizeof(PACKET) == sizeof(char) + sizeof(int), "error, unexpected padding !");

template<typename T> void Swap(T& a, T& b)
{
    static_assert(is_copy_constructible<T>::value, "error. T is not copyable");
    T tmp = a;
    a = b;
    b = tmp;
}

int main()
{
    mutex m1;
    mutex m2;
    Swap(m1, m2);
}
```

Swap이 실행되려면 mutex의 복사생성자가 필요 -> 그러나 mutex에는 없어서 error 가 나는데 error message가 너무 김

static_assert 를 이용해 복사생성자 조사

T is not copyable 이라는 error message로 효율적을 보여줌

---

> non member begin/end

STL의 반복자를 꺼내는 begion, end

``` cpp
#include <iostream>
#include <list>
#include <vector>

using namespace std;

template<typename T> void show(T& c)
{
    auto p1 = c.begin();
    auto p2 = c.end();

    while(p1 != p2)
    {
        cout << *p1 << endl;
        ++p1;
    }
}

int main()
{
    // list<int> c = {1,2,3};
    // vector<int> c = {1,2,3};

    int x[3] = {1,2,3}; // error . 배열에는 begin, end 없음
    show(c);
}
```

STL 컨테이너 뿐 아니라 진짜 배열도 꺼낼 수 있도록 해보자

``` cpp
#include <iostream>
#include <list>
#include <vector>

using namespace std;

template<typename T> void show(T& c)
{
    auto p1 = begin(c);
    auto p2 = end(c);

    while(p1 != p2)
    {
        cout << *p1 << endl;
        ++p1;
    }
}

int main()
{
    list<int> c = {1,2,3};
    show(c);

    vector<int> c2 = {1,2,3};
    show(c2);

    int x[3] = {1,2,3};
    show(x);
}
```

#### 반복자를 꺼내는 2가지 방법
- 컨테이너의 멤버함수 사용 -> raw 배열에는 사용할 수 없다
- 멤버가 아닌 함수 사용 => STL의 컨테이너 뿐 아니라, raw 배열에도 사용할 수 있다.

#### 일반 함수 begin()/end()
- C++ 11 부터 제공
- iterator 헤더 파일
- STL 컨테이너 뿐 아니라 raw 배열에서 사용가능한 알고리즘을 만들 수 있다.
- C++11 : begin(), end()
- C++14 : cbegin(), cend(), rbegin(), rend(), crbegin(), crend()

**결론 : 일반 함수를 써서 반복자를 꺼내자!**

---

#### 일반 함수 begin, end의 원리

``` cpp
#include <iostream>
#include <list>
#include <vector>

using namespace std;

// container version.
template<typename C>
constexpr auto begin(C& c) -> decltype(c.begin())
{
    return c.begin();
}

template<typename C>
constexpr auto end(C& c) -> decltype(c.end())
{
    return c.end();
}

// array version
template<typename T, std::size_t N>
constexpr T* begin(T(&arr)[N])
{
    return arr;
}

template<typename T, std::size_t N>
constexpr T* end(T(&arr)[N])
{
    return arr + N;
}

int main()
{
    list<int> s = {1,2,3};
    int x[3] = {1,2,3};

    auto p1 = begin(s);
    auto p2 = begin(x);
}
```

인자가 STL 컨테이너를 받는 것과 배열을 받는 버전을 별도로 제공

---

> range for

``` cpp
#include <iostream>
#include <list>
using namespace std;

int main()
{
    // int x[10] = {1,2,3,4,5,6,7,8,9,10};
    list<int> s = {1,2,3,4,5,6,7,8,9,10};

    for (auto& n : x) // 배열, STL 컨테이너..
        cout << n << endl;
}
```

#### range for
- C++11에서 도입된 문법
- STL 컨테이너, raw 배열에 있는 모든 요소에 접근하기 위한 편리한 방법
- java, C# 등의 foreach()와 유사한 개념

--- 

#### range for 원리
- begin()/end()를 사용해서 얻어진 반복자를 통해서 요소에 접근하는 것

``` cpp
#include <iostream>
#include <list>
using namespace std;

int main()
{
    list<int> s = {1,2,3,4,5,6,7,8,9,10};

    //for (auto& n : x)
    //    cout << n << endl;
    // 컴파일러가 위의 코드를 아래와 같이 변환
    for (auto p = begin(s); p != end(s); ++p)
    {
        auto& n = *p;

        cout << n << endl;
    }
}
```

``` cpp
#include <iostream>
#include <list>
using namespace std;

struct Point3D
{
    int x = 1;
    int y = 2;
    int z = 3;
};

int* begin(Point3D& p3) { return &(p3.x); }
int* end(Point3D& p3) { return &(p3.z) + 1; }

int main()
{
    Point3D p3;

    for (auto& n : p3)
        cout << n << endl;
}
```
#### User define type과 range for
- STL이나 배열이 아닌 사용자가 만든 컨테이너의 경우, begin()/end() 함수를 일반함수 또는 멤버함수로 제공하면 range for에 넣을 수 있다.
