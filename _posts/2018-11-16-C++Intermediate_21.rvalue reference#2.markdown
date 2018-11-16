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