---
layout: post
title:  "C++Intermediate&nbsp;9.&nbsp;C++기본문법_conversion#1"
date:   2018-10-30 04:18:15 +0700
categories: [c++]
---

codenuri 강석민 강사 강의 내용기반으로 정리한 내용입니다.

---

> 변환연산자와 변환생성자

```cpp
#include <iostream>
using namespace std;

class Point
{
    int x, y;
public:
    Point() : x(0), y(0) {}
    Point(int a, int b) : x(a), y(b) {}
};

int main()
{
    int n = 3;
    double d = n; // 암시적 형변환 발생.

    Point p1(1, 2);
    n = p1; // p1.operator int()

    cout << n << endl;
}
```

p1.operator int 를 만들었다면 p1의 Point 가 int로 변환할 수 있음

-> 이런 것을 변환연산자

#### 변환연산자

객체를 다른 타입으로 변환할 때 호출된다.

특징 : 리턴 타입을 표기하지 않는다.

```cpp
#include <iostream>
using namespace std;

class Point
{
    int x, y;
public:
    Point() : x(0), y(0) {}
    Point(int a, int b) : x(a), y(b) {}

    int operator int()
    {
        return x;
    }
};

int main()
{
    int n = 3;
    double d = n; // 암시적 형변환 발생.

    Point p1(1, 2);
    n = p1; // p1.operator int()

    cout << n << endl; // 1
}
```

컴파일러가 n = p1을 보면 int 로 변환하려고 하는데 이를 변환 연산자를 통해 수행하려고 함

---

#### 변환 생성자

```cpp
#include <iostream>
using namespace std;

class Point
{
    int x, y;
public:
    Point() : x(0), y(0) {}
    Point(int a, int b) : x(a), y(b) {}

    int operator int()
    {
        return x;
    }
};

int main()
{
    int n = 3;
    Point p(1, 2);

    n = p;
    p = n;
}
```

n = p; 는 Point -> int 로 p.operator int()

p = n; 은 int -> Point 로 n.operator Point() 가 있으면 된다.

하지만 n은 사용자정의 타입이 아니다.

```cpp
#include <iostream>
using namespace std;

class Point
{
    int x, y;
public:
    Point() : x(0), y(0) {}
    Point(int a, int b) : x(a), y(b) {}

    int operator int()
    {
        return x;
    }
};

int main()
{
    Point p1;
    Point p2(1,1);

    int n = 3;
    Point p(1, 2);

    n = p;
    p = n;
}
```

int가 2개 있으면 Point를 만들 수 있다. -> Point p2(1, 1);

int 가 하나도 없더라도 Point를 만들 수 있다 -> Point p1

p = n; 은 Point 생성자에 인자가 하나 있는 것을 만들어야함


```cpp
#include <iostream>
using namespace std;

class Point
{
    int x, y;
public:
    Point() : x(0), y(0) {}
    Point(int a, int b) : x(a), y(b) {}

    // 인자가 한 개인 생성자 - 변환생성자
    Point(int a) : x(a), y(0) {} 

    int operator int()
    {
        return x;
    }
};

int main()
{
    Point p1;
    Point p2(1,1);

    int n = 3;
    Point p(1, 2);

    n = p;
    p = n;
}
```

변환생성자 : 다른 타입이 Point class로 변환 되게 한다.

생성자 중에서 인자가 하나 같는 생성자를 만들면 변환에 사용될 수 있다.

#### 정 리
1. Point -> int로 변환하려면 변환 연산자 p.operator int()
2. int -> Point 로 변환하려면 변환 생성자 Point(int)

---

> 변환의 장단점

#### 변환의 장점

``` cpp
class OFile
{
    FILE* file;
public:
    OFile(const char* filename, const char* mode = "wt")
    {
        file = fopen(filename, mode);
    }
    ~OFile()
    {
        fclose(file);
    }

    // 변환연산자
    operator FILE*() {return file;}
};

int c_main()
{
    FILE* f = fopen("a.txt", "wt");
    fputs("hello", f);

    if (...)
        return false; // c에서는 이러면 해지도 해주어야함

    fclose(f);
}

int main()
{
    OFile f("a.txt"); // 생성자에서 자원을 할당하고, 소멸자에서 자원을 해제 => RAII

    // C 함수를 사용해서 파일 작업 -> C++로 자원관리 쉽게하고 C함수도 자유롭게 사용하게 해보자
    fputs("hello", f);
    fprintf(f, "n=%d", 10); // OFile => FILE*로 암시적 변환되면 가능.
    // f.operator FILE*() 변환연산자를 만들면됨


    // 대표 예제
    String s1 = "hello";
    char s2[10];

    strcpy(s2, s1); // String -> const char* 암시적 변환 필요
}
```

RAII : Resource Acquision Is Initialization

fclose 도 자동으로 하면서 기존의 모든 C 함수를 다시 다 쓸 수 있음

---

#### 변환의 단점

``` cpp
class OFile
{
    FILE* file;
public:
    OFile(const char* filename, const char* mode = "wt")
    {
        file = fopen(filename, mode);
    }
    ~OFile()
    {
        fclose(file);
    }
    operator FILE*() {return file;}
};

void foo(OFile f) {}

int main()
{
    OFile f("a.txt");
    foo(f); // ok..

    foo("hello"); // error 발생하지 않음
}
```

hello는 정체가 const char*임

foo한테 넘어갈 때 error 가 안나는 것은 const char*가 OFile로 암시적 변환 발생

변환생성자로 변환할텐데, OFile의 생성자는 인자2개처럼 보이지만 2번째 생성자인 mode는 default 값이 존재하므로 변환생성자로 사용될 수 있음

잘못된 코드가 에러가 안나오는 문제가 있음

생성자로 인한 암시적 변환은 매우 위험함 -> explicit 생성자 이용

---

##### explicit 생성자

인자가 한개인 생성자가 암시적 변환을 허용하는 것을 막는다.

``` cpp
class OFile
{
    FILE* file;
public:
    explicit OFile(const char* filename, const char* mode = "wt")
    {
        file = fopen(filename, mode);
    }
    ~OFile()
    {
        fclose(file);
    }
    operator FILE*() {return file;}
};

void foo(OFile f) {}

int main()
{
    OFile f("a.txt");
    foo(f); // ok..

    // foo("hello"); // error
    foo(static_cast<OFile>("hello")); // 명시적 변환은 됨
}
```

---

> explicit

explicit 와 객체의 초기화 기술

``` cpp
class Test
{
    int value;
public:
    explicit Test(int n) : value(n) {}
};

int main()
{
    // 아래 2줄의 차이점은 ?
    Test t1(5); // 인자가 한개인 생성자 호출 -> direct initialization

    Test t2 = 5; // copy initialization
}
```

#### Test t2=5 가 하는일 
1. 변환생성자를 사용해서 5를 가지고 Test 의 임시 객체 생성
2. 임시객체를 복사 생성자를 사용해서 t2에 복사

explicit 를 붙이면 Test t2=5 는 error 발생 -> =로 초기화 될 수 는 없다.

---

STL을 잠깐 보자

``` cpp
#include <string>
#include <memory>
using namespace std;

int main()
{
    // STL의 string 클래스는 생성자가 explicit 가 아님
    string s1("hello");  // ok
    string s2 = "hello"; // ok

    shared_ptr<int> p1 = new int; // error 생성자가 explicit
    shared_ptr<int> p2(new int);  // ok..
}

```

= 이 안되면 생성자가 explicit 로 되어 있다로 이해하면됨

---

> make nullptr

``` cpp
int main()
{
    int n1 = 10;    // ok
    void* p1 = 10;  // error.

    int n2 = 0;     // ok
    void* p2 = 0;   // ok. 0은 정수지만 포인터로 암시적 형변환된다.
}
```

``` cpp
#include <iostream>
using namespace std;

void foo(int n) {cout << "int" << endl;}
void foo(void* p) {cout << "void*" << endl;}

void goo(char* p) {cout << "goo" << endl;}

int main()
{
    foo(0);
    foo((void*)0);

#define NULL (void*)0
    foo(NULL);

    goo(NULL); // void* -> char* 로의 암시적 변환 필요.
    // C: ok
    // C++: 암시적 변환 안됨

#ifdef __cpluscplus
    #define NULL 0
#else
    #define NULL (void*)0
#endif
}
```

C++에서 정수 0은 있는데 완벽한 pointer 0이 없어서 위와같은 혼란 발생

---

변환을 사용해서 pointer 0을 만들어보자

``` cpp
#include <iostream>
using namespace std;

void foo(int n) {cout << "int" << endl;} // 1
void foo(void* p) {cout << "void*" << endl;} // 2

void goo(char* p) {cout << "goo" << endl;} // 3

struct xnullptr_t
{
    // operator void*() { return 0; }
    template<typename T>
    operator T*() { return 0; } // 모든포인터의 타입을 암시적 형변환되어 goo 출력됨
};
xnullptr_t xnullptr; // 포인터 0

int main()
{
    foo(0); // 1
    foo(xnullptr); // 2. xnullptr_t -> void*로의 암시적 변환 필요
    // xnullptr.operator void*()

    goo(xnullptr); // 3 goo

    int n = 0;
    double* p = xnullptr; // double의 포인터로 변환됨
}
```

---

``` cpp
// C++ 11 : nullptr

int main()
{
    int n = 0;
    double* p1 = nullptr; // C++11 의 포인터 0

    nullptr_t a = nullptr;

    int* p = a;
}
```

nullptr 은 정확히 nullptr_t라는 데이터 타입의 변수임

