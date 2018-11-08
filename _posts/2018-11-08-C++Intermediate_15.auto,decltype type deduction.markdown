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

> decltype
