---
layout: post
title:  "C++Intermediate&nbsp;28.&nbsp;inline function"
date:   2018-11-29 02:18:15 +0700
categories: [c++]
---

codenuri 강석민 강사 강의 내용기반으로 정리한 내용입니다.

---

> inline function

``` cpp
#include <iostream>
using namespace std;

int Add1(int a, int b) { return a + b; }
inline int Add2(int a, int b) { return a + b; }

int main()
{
    int n1 = Add1(1, 2); // 호출
    int n2 = Add2(1, 2); // 치환
}
```

inline 치환 장점 : 속도가 빠르다.

inline 치환 단점 : 목적코드가 커진다.

cl inline1.cpp /FAs /Ob1 => inline1.asm 으로 어셈 코드 확인해보기

/Ob1 -> inline 치환을 적용해달라

목적코드가 커지는 것은 함수가 긴 경우에 해당함, 간단한 함수의 경우 오히려 목적코드의 크기는 줄어듬

---

> 인라인 함수와 함수포인터

1. 인라인 함수 문법은 컴파일 시간 동작한다.
2. 인라인 함수라도 함수포인터에 담아서 사용하면 호출된다.

``` cpp
#include <iostream>
using namespace std;

int Add1(int a, int b) { return a + b; }
inline int Add2(int a, int b) { return a + b; }

int main()
{
    int n1 = Add1(1, 2); // 호출
    int n2 = Add2(1, 2); // 치환

    int(*f)(int, int);

    f = &Add2;

    int n3 = f(1, 2); // 호출 ? 치환 ?
                      // 호출하는 코드가 나옴
}
```

---

> 인라인 함수와 linkage

#### add.h

``` cpp
int Add1(int a, int b);
inline int Add2(int a, int b);
```

#### add.cpp

``` cpp
int Add1(int a, int b) { return a + b; }
inline int Add2(int a, int b) { return a + b; }
```

#### inline3.cpp

``` cpp
#inlcude "add.h"
int main()
{
    Add1(1,2);
    Add2(1,2);
}
```

-> 컴파일시 error

LNK2019 에서 Add2를 찾지 못한다고 error 발생함

add와 inline3.cpp 따로 컴파일 하는데 인라인 함수는 치환하는 것임

결국 include 의 의미는 add.h를 inline3.cpp에 가져다 놓는 개념밖에 없는 것임

``` cpp
// #inlcude "add.h"
int Add1(int a, int b);
inline int Add2(int a, int b);
int main()
{
    Add1(1,2);
    Add2(1,2);
}
```

Add2가 어떻게 생긴지 완전한 모양이 없기 때문에 치환할 수 없음

Add1은 호출이라 컴파일러가 컴파일 할 때 call Add1 을 부르는데 call(4바이트 빈공간) 주소를 넣어주고, 링커가 나중에 채워주면됨(어떤 함수가 몇번지에 있는지 찾는 것이 링커의 역할)

해결책은 inline 함수는 헤더에 구현부를 만들면됨


#### add.h

``` cpp
int Add1(int a, int b);
inline int Add2(int a, int b) { return a + b; }
```

인라인 함수는 헤더에 구현부까지 만들어 주는 것이 원칙임

-> 이런 것을 internal linkage(같은 파일에 있어야 쓸 수 있다)
