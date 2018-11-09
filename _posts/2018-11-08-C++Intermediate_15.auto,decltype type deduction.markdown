---
layout: post
title:  "C++Intermediate&nbsp;15.&nbsp;auto, decltype type deduction"
date:   2018-11-08 04:18:15 +0700
categories: [c++]
---

codenuri 강석민 강사 강의 내용기반으로 정리한 내용입니다.

---

> auto

```cpp
#include <iostream>
using namespace std;

int main()
{
    int n = 10;
    int& r = n;

    auto a = r; // a? int ? int&
    a = 30;

    cout << n << endl; // 30 ? 10
}
```

#### auto를 값 타입으로 사용할 때

우변 수식이 가진 reference, const, volatile 속성ㅇ르 제거하고 타입 결정

#### auto를 참조 타입으로 사용할 때

우변 수식이 가진 reference 속성만 제거되고 const, volatile 속성은 유지된다.

#### 주의사항

변수 자체의 const 속성만 제거된다.

``` cpp
int main()
{
    int n = 10;
    int& r = n;
    const int c = n;
    const int& cr = c;

    // auto : 값 복사 방식
    auto a1 = n; // int
    auto a2 = r; // int
    auto a3 = c; // const 속성 무시 해서 그냥 int
    auto a4 = cr; // int

    auto& a5 = n; // auto : int, a5 : int&
    auto& a6 = r; // auto : int, a6 : int&
    auto& a7 = c; // auto : int, a7 : int& -> int& a7 = c; 로 const 를 int&로 가리킬 수 없음
                  // auto : const int, a7 : const int & 로 결정됨
    auto& a8 = cr; // auto : const int, a8 : const int&

    const char* s1 = "hello"; // s1 자체는 const 아님, s1을 따라가면 const
    auto a9 = s1; // const char*

    const char* const s2 = "hello"; // s2 앞에 const 는 변수의 속성이니까 제거됨ㄴ
    auto a10 = s2; // const char*
}
```

---

> decltype type deduction

``` cpp
int main()
{
    int n = 0;
    int* p = &n;

    decltype(n) d1; // int
    decltype(p) d2; // int*

    // (수식) : 수식이 lvalue라면 참조, 아니면 값 타입
    decltype(*p) d3; // *p = 10; int&
    decltype((n)) d4; // (n) = 10; int&

    decltype(n+n) d5; // n+n = 10; error. 이므로 int
    decltype(++n) d6; // ++n = 10; ok.. int&
    decltype(n++) d7; // n++ = 10; error. int

    int x[3] = {1,2,3};

    decltype(x[0]) d8; // x[0] = 10; ok int&
    auto a1 = x[0]; // int
}
```

---

``` cpp
int x = 10;

int& foo(int a, int b)
{
    return x;
}

int main()
{
    auto ret1 = foo(1, 2); // int

    // 평가되지 않는 표현식(unevaluated expression)
    decltype(foo(1,2)) ret2 = foo(1, 2); // 진짜 함수호출은 아니고 함수 실행 결과로 나오는 타입 -> int&

    // C++14
    decltype(auto) ret3 = foo(1,2); // int&
}
```

---

> array name

``` cpp
int main()
{
    int x[3] = {1,2,3};

    int *p = x; // 배열의 이름은 배열의 주소이다 ??
}

```

``` cpp
int main()
{
    int n; // 변수 이름: n , 타입: int
    int *p1 = &n; // 

    double d; // 변수 이름: d, 타입: double
    double *p2 = &d;

    int x[3] = {1,2,3}; // 변수이름: x, 타입: int[3]

    // 배열 x의 주소..
    int (*p3)[3] = &x; // 배열의 주소

    int *p4 = x; // 배열의 주소 아님..
}

```

``` cpp
int main()
{
    int n1 = 10;
    int n2 = n1; // 모든 변수는 자신과 동일한 타입의 변수로 초기화 될 수 있다.

    double d1 = 3.4;
    double d2 = d1;

    int x1[3] = {1,2,3}; // x1의 타입 : int[3]
    int x2[3] = x1; // error. 배열은 자신과 동일한 타입의 배열로 복사가 안됨..

    int *p1 = x1; // 배열의 이름은 첫번째 요소의 주소로 암시적 형변환 된다.
}
```

``` cpp
#include <stdio.h>

int main()
{
    int x[3] = {1,2,3};

    int(*p1)[3] = &x; // 배열의 주소

    int *p2 = x; // 배열의 이름이 배열의 첫번째 요소의 주소로 암시적 형변환 도 표현

    printf("%p %p\n", p1, p1+1); // 12
    printf("%p %p\n", p2, p2+1); // 4

    // p1 : 배열의 주소, *p1 : 배열
    (*p1)[0] = 10;

    // p2 : 요소의 주소, (int*)
    *p2 = 10;
}
```

p1은 p1+1 을 하게 되면 int[3]을 가르키니까 12가 증가

p2는 p2+1 로 배열 첫번째 int 값을 가르키니까 4가 증가

---

> auto misc

``` cpp
#include <iostream>
#include <typeinfo>
#include <vector>
using namespace std;

int main()
{
    int x[3] = {1,2,3}; // x: int[3]

    auto a1 = x;    // int a1[3] = x; compile error, int* a1 = x;
    auto& a2 = x;   // int(&a2)[3] = x; //ok, a2: int(&)[3]

    decltype(x) d; // int[3]

    cout << typeid(a1).name() << endl; //int*
    cout << typeid(a2).name() << endl; //int(&)[3]
    cout << typeid(d).name() << endl; //int[3]

    // -------------------------------------------------------------

    auto a3 = 1; // int
    auto a4{1}; // int
    auto a5 = {1}; // int ? initializer_list => initializer_list

    cout << typeid(a4).name() << endl;
    cout << typeid(a5).name() << endl;

    // -------------------------------------------------------------

    vector<int> v1(10, 0);
    auto a6 = v1[0]; // int

    vector<bool> v2(10, 0);
    auto a7 = v2[0]; // bool이 아니라 다른게 나옴 -> std::_Bit_reference(temporary proxy 참고)
}
```
