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

