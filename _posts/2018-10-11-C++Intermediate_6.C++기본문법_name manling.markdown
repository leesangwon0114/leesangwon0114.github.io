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

#### square.h

``` cpp
int square(int);

```
#### square.c

``` cpp
int square(int n)
{
    return n * n;
}
```

#### main.cpp

``` cpp
#include "square.h"

int main()
{
    square(3);
}
```

컴파일러는 확장자를 보고 각 문법을 파싱하는데 square.c는 c문법 기준으로 square가 manling 안된 문법으로 만듬

main.cpp는 C++문법 기준으로 manling 된 문법으로 만드므로 main.obj에서 함수를 찾을 수 없는 error 발생

![Alt text](/static/img/C++Intermediate/6.2.PNG)

#### square.h에 extern "C" 삽입

#### square.h

``` cpp
extern "C"  // C++ 컴파일러에게 C 처럼 해석해 달라.
int square(int);
```

앞의 예제에서 main.cpp를 main.c로 바꾸면 문제 발생

#### main.c

``` cpp
#include "square.h"

int main()
{
    square(3);
}
```

main이 .c니까 square.h를 포함하여 컴파일하는데 c 컴파일러는 extern "C" 문법이 뭔지 알 수 없음

---

#### 최종 코드(__cpluscplus 사용)

#### square.h

``` cpp
#ifdef __cpluscplus
extern "C" {
#endif
    int square(int);
#ifdef __cpluscplus
}
#endif
```
#### square.c

``` cpp
int square(int n)
{
    return n * n;
}
```

#### main.cpp, main.c 둘 다 컴파일 가능해짐

``` cpp
#include "square.h"

int main()
{
    square(3);
}
```

---

> 함수이름과 함수 주소

``` cpp
#include <cstdio>

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
    // printf("%p\n", &square); // error

    printf("%p\n", static_cast<int(*)(int)>(&square)); // ok.

    // auto p = &square; // error

    int(*f)(int) = &square; // ok.
}
```

오버로딩된 함수가 있을 경우 주소연산자만을 통해 구할 수 없고 정확히 함수 포인터를 이용해야함

---

함수와 함수주소

``` cpp
#include <iostream>
#inlcude <typeinfo>
using namespace std;

void foo(int a)
{

}

int main()
{
    void(*f1)(int) = &foo; // ok. 함수 주소 꺼내기
    void(*f2)(int) = foo;  // 함수 이름은 함수 주소로 암시적 형변환

    typedef void(*PF)(int); // 함수포인터타입
    typedef void F(int); // 함수 타입..

    cout << typeid(&foo).name() << endl; // void(*)(int)
    cout << typeid(foo).name() << endl; // void(int)
}
```

g++에서 a.exe | c++filt -t 로 하면 타입 정확히 볼 수 있음

![Alt text](/static/img/C++Intermediate/6.3.PNG)

---

> 함수 찾는 순서

#### exactly matching - floo(float)

``` cpp
struct FLOAT
{
    FLOAT(float) {} // 변환 생성자, float=>FLOAT로 변환 허용
};

template<typename T>
void foo(T) { cout << "T" << endl; }
void foo(int) { cout << "int" << endl; }
void foo(double) { cout << "double" << endl; }
void foo(float) { cout << "float" << endl; }
void foo(FLOAT) { cout << "FLOAT" << endl; }
void foo(...) { cout << "..." << endl; }

int main()
{
    float f = 3.4f;
    foo(f);
}
```

float 가 불림

#### template = foo(T)

``` cpp
struct FLOAT
{
    FLOAT(float) {} // 변환 생성자, float=>FLOAT로 변환 허용
};

template<typename T>
void foo(T) { cout << "T" << endl; }
void foo(int) { cout << "int" << endl; }
void foo(double) { cout << "double" << endl; }
// void foo(float) { cout << "float" << endl; }
void foo(FLOAT) { cout << "FLOAT" << endl; }
void foo(...) { cout << "..." << endl; }

int main()
{
    float f = 3.4f;
    foo(f);
}
```

template 가 불림

#### promotion - foo(double)

``` cpp
struct FLOAT
{
    FLOAT(float) {} // 변환 생성자, float=>FLOAT로 변환 허용
};

template<typename T>
// void foo(T) { cout << "T" << endl; }
void foo(int) { cout << "int" << endl; }
void foo(double) { cout << "double" << endl; }
// void foo(float) { cout << "float" << endl; }
void foo(FLOAT) { cout << "FLOAT" << endl; }
void foo(...) { cout << "..." << endl; }

int main()
{
    float f = 3.4f;
    foo(f);
}
```

데이터의 손실이 없는 방향으로 변환됨


#### standard conversion - foo(int)

``` cpp
struct FLOAT
{
    FLOAT(float) {} // 변환 생성자, float=>FLOAT로 변환 허용
};

template<typename T>
// void foo(T) { cout << "T" << endl; }
void foo(int) { cout << "int" << endl; }
// void foo(double) { cout << "double" << endl; }
// void foo(float) { cout << "float" << endl; }
void foo(FLOAT) { cout << "FLOAT" << endl; }
void foo(...) { cout << "..." << endl; }

int main()
{
    float f = 3.4f;
    foo(f);
}
```

데이터의 손실이 되더로도 표준 타입끼리는 암시적 형변환이 된다.

#### user define conversion - foo(FLOAT)

``` cpp
struct FLOAT
{
    FLOAT(float) {} // 변환 생성자, float=>FLOAT로 변환 허용
};

template<typename T>
// void foo(T) { cout << "T" << endl; }
// void foo(int) { cout << "int" << endl; }
// void foo(double) { cout << "double" << endl; }
// void foo(float) { cout << "float" << endl; }
void foo(FLOAT) { cout << "FLOAT" << endl; }
void foo(...) { cout << "..." << endl; }

int main()
{
    float f = 3.4f;
    foo(f);
}
```

변환연산자나 변환 생성자 사용

#### variadic argument - foo(...)

``` cpp
struct FLOAT
{
    FLOAT(float) {} // 변환 생성자, float=>FLOAT로 변환 허용
};

template<typename T>
// void foo(T) { cout << "T" << endl; }
// void foo(int) { cout << "int" << endl; }
// void foo(double) { cout << "double" << endl; }
// void foo(float) { cout << "float" << endl; }
// void foo(FLOAT) { cout << "FLOAT" << endl; }
void foo(...) { cout << "..." << endl; }

int main()
{
    float f = 3.4f;
    foo(f);
}
```

변환을 통해 갈 수 있는 곳이 없을 때 마지막으로 가변인자 사용
