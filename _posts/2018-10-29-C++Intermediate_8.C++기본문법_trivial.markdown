---
layout: post
title:  "C++Intermediate&nbsp;8.&nbsp;C++기본문법_trivial"
date:   2018-10-29 04:18:15 +0700
categories: [c++]
---

codenuri 강석민 강사 강의 내용기반으로 정리한 내용입니다.

---

> trivial 개념

```cpp
#include <iostream>
#include <type_traits>
using namespace std;

// Trivial : 사소한, 하찮은, 하는 일이 없다.

// Trivial default Constructor : 기본생성자가 하는 일이 없는 경우.

class A
{
public:
    virtual void foo() {}
};

int main()
{
    cout << is_trivially_constructible<A>::value << endl;
}
```

0 이 출력됨

가상함수가 있으면 가상함수 초기화를 하기 때문에 default 생성자가 하는 일이 있게됨

virtual 을 빼면 ?

```cpp
#include <iostream>
#include <type_traits>
using namespace std;

class A
{
public:
    void foo() {}
};

int main()
{
    cout << is_trivially_constructible<A>::value << endl;
}
```

1 이 출력됨

```cpp
#include <iostream>
#include <type_traits>
using namespace std;

class A
{
public:
    A(); // {}
    void foo() {}
};

int main()
{
    cout << is_trivially_constructible<A>::value << endl;
}
```

컴파일러는 선언만 보고 조사하기 때문에 구현 쪽에 만들었는지는 판단 불가능

컴파일러 입장에서는 default 생성자가 하는 일이 있다보고봄

0 이 출력됨

```cpp
#include <iostream>
#include <type_traits>
using namespace std;

class A
{
public:
    A() = default;
    void foo() {}
};

int main()
{
    cout << is_trivially_constructible<A>::value << endl;
}
```

default라고 생성자에 명시적으로 주면 1이 출력됨

---

> trivial 활용

``` cpp
#include <iostream>
#include <type_traits>

using namespace std;

// 왜 trivial 개념이 필요 한가?

// 안드로이드 프레임워크..
template<typename T> void copy_type(T* dst, T* src, int sz)
{
    if (is_trivially_copyable<T>::value) {
        cout << "복사 생성자가 trivial 할때" << endl;
        memcpy(dst, src, sizeof(T) * sz);
    } else {
        cout << "복사 생성자가 trivial 하지 않을 때" << endl;s
        while(sz--)
        {
            new(dst) T(*src); // 복사 생성자 호출
            ++dst, ++src;
        }
    }
    
}

struct People
{
    People() {}
    People(const People&) {} // !

};

int main()
{
    char s1[10] = "hello"; // People p1[10];
    char s2[10] = { 0 };   // People p2[10];

    // strcpy(s2, s1); // char 만 복사되니 모든 타입을 복사해보자

    // 복사 생성자가 trivial 할 때
    copy_type(s1, s2, 10); // 모든 타입의 배열을 복사하는 함수

    // 복사 생성자가 trivial 하지 않을 때
    People s3[10];
    Pepple s4[10];
    copy_type(s3, s4, 10);
}
```

copy_type의 다른 버전(enable_if 사용)

if else 대신에 다른 함수로 분리하는 것이 좋은 코드임

``` cpp
#include <iostream>
#include <type_traits>

using namespace std;

// 왜 trivial 개념이 필요 한가?

template<typename T>
typename enable_if<is_trivially_copyable<T>::value>::type copy_type(T* dst, T* src, int sz)
{
    cout << "복사 생성자가 trivial 할때" << endl;
    memcpy(dst, src, sizeof(T) * sz); 
}

template<typename T>
typename enable_if<!is_trivially_copyable<T>::value>::type copy_type(T* dst, T* src, int sz)
{
    cout << "복사 생성자가 trivial 하지 않을 때" << endl;s
    while(sz--)
    {
        new(dst) T(*src); // 복사 생성자 호출
        ++dst, ++src;
    }
}

struct People
{
    People() {}
    People(const People&) {} // !

};

int main()
{
    char s1[10] = "hello"; // People p1[10];
    char s2[10] = { 0 };   // People p2[10];

    // strcpy(s2, s1); // char 만 복사되니 모든 타입을 복사해보자

    // 복사 생성자가 trivial 할 때
    copy_type(s1, s2, 10); // 모든 타입의 배열을 복사하는 함수

    // 복사 생성자가 trivial 하지 않을 때
    People s3[10];
    Pepple s4[10];
    copy_type(s3, s4, 10);
}
```

---

> trivial 조건

``` cpp
// trivial default constructor 의 조건
struct Base
{
    // Base() {}
};

class A : public Base
{
    // Data data; // 객체형 멤버가 없어야.
    int n = 10;
public:
    // A() {}               // user define 생성자가 없어야
    // virtual void foo() {} // 가상함수가 없어야
};

int main()
{
    cout << is_trivially_constructible<A>::value << endl;
}
```

아직 0이 출력됨(trivial 하지 않다고)

int n = 10;을 초기화해주는 코드때문에 안됨

``` cpp
// trivial default constructor 의 조건
struct Base
{
    // Base() {}
};

class A : public Base
{
    // Data data; // 객체형 멤버가 없어야.
    int n;
public:
    // A() {}               // user define 생성자가 없어야
    // virtual void foo() {} // 가상함수가 없어야
};

int main()
{
    cout << is_trivially_constructible<A>::value << endl;
}
```

이제서야 1이 출력됨

---

#### Trivial default constructor

The default constructor for class T is trivial if all of the following is true

1. The constructor is not user-provied
2. T has no virtual member functions
3. T has no virtual base classes
4. T has no non-static members with default initializers(since C++11)
5. Every direct base of T has a trivial default constructor
6. Every non-static member of class type has a trivial default constructor

