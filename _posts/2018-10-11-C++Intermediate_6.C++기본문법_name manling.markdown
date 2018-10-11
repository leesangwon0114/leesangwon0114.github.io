---
layout: post
title:  "C++Intermediate&nbsp;6.&nbsp;C++기본문법_name manling"
date:   2018-10-11 04:18:15 +0700
categories: [c++]
---

codenuri 강석민 강사 강의 내용기반으로 정리한 내용입니다.

---

> name manling

1. 컴파일러가 컴파일 시간에 심볼의 이름을 변경하는 현상
2. 함수 오버로딩, namespace, template 등의 문법

```cpp
#include <iostream>
using namespace std;

int square(int n)
{
    return n * n;
}

double square(double d)
{
    return d * d;
}

int main()
{
    square(3);
    square(3.3);
}
```

어셈블리 소스로 정확히 파악 가능

1. g++ file.cpp -S
2. cl file.cpp /FAs

![Alt text](/static/img/C++Intermediate/6.1.PNG)

이런 name mangling 으로 인해 C하고 C++하고 호환성 문제 발생 -> extern

> extern "C"

