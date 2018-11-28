---
layout: post
title:  "C++Intermediate&nbsp;27.&nbsp;reference wrapper"
date:   2018-11-28 02:18:15 +0700
categories: [c++]
---

codenuri 강석민 강사 강의 내용기반으로 정리한 내용입니다.

---

> std::bind & std::ref

``` cpp
#include <iostream>
#include <functional>
using namespace std;

void foo() {}
void goo(int a) {}

int main()
{
    void(*f)() = &foo;
    // f = &goo; // error

    function<void()> f2 = &foo;

    f2(); // foo()

    f2 = bind(&goo, 5);

    f2(); // goo(5);
}
```

#### 핵심 정리

#### 1. std::function
- 함수 포인터 역할을 하는 템플릿
- 일반 함수 뿐 아니라 함수 객체, 람다 표현식 등도 담을 수 있다.
- bind 와 함께 사용하면 인자의 개수가 다른 함수(함수 객체)도 사용할 수 있다.

---

#### 2. std::bind
- 함수 또는 함수 객체의 인자를 고정할 때 사용한다.
- 인자의 값을 고정 할 때 값 방식을 사용한다.
- 참조로 인자를 고정하려면 ref() 또는 cref()를 사용한다.

``` cpp
#include <iostream>
#include <functional>
using namespace std;

void goo(int& a) { a = 30; }

int main()
{
    int n = 0;

    function<void()> f;

    f = bind(&goo, n); // 값으로 고정

    f(); // goo(n);

    cout << n << endl; // 0
}

출력이 0이 나옴

``` cpp
#include <iostream>
#include <functional>
using namespace std;

void goo(int& a) { a = 30; }

int main()
{
    int n = 0;

    function<void()> f;

    f = bind(&goo, ref(n)); // 참조로 고정

    f(); // goo(n);

    cout << n << endl; // 30
}
```

#### 아예 n을 보낼 때 참조를 보내면 되는데 왜 값으로 보냈을까?

``` cpp
#include <iostream>
#include <functional>
using namespace std;

void goo(int& a) { a = 30; }

int main()
{

    function<void()> f;

    // 다른 구간이라 보면
    {
        int n = 0;

        f = bind(&goo, n);
    }

    // n 을 참조로 보내면 블럭 벗어나면 파괴되버림...

    f(); // goo(n);

    cout << n << endl; // 30
}
```

---

> std::reference_wrapper

``` cpp
#include <iostream>
using namespace std;

void foo(int& a) { a = 30; }

template<typename F, typename T>
void chronometry(F f, T& arg)
{
    f(arg);
}

int main()
{
    int n = 0;
    chronometry(&foo, n);

    function<void()> f;
    f = bind(&foo, n);
    // 아래 호출 해야 n이 전달됨(n 이 전달되기 전에 파괴될 가능성이 있음)
    
    f();
}
```

ref 로 보내야함

#### chronometry 의 인자를 값으로 받으면서 해결해보기

``` cpp
#include <iostream>
using namespace std;

void foo(int& a) { a = 30; }

template<typename F, typename T>
void chronometry(F f, T arg)
{
    f(arg);
}

int main()
{
    int n = 0;
    chronometry(&foo, &n);
}
```

핵심 1. 주소를 전달한다.

핵심 2. 포인터가 참조로 암시적 형변환 되면 가능하다.

---

#### 참조로 변환 가능한 포인터

``` cpp
#include <iostream>
using namespace std;

template<typename T> struct xreference_wrapper
{
    T* ptr;
public:
    xreference_wrapper(T& r) : ptr(&r) {}

    operator T&() { return *ptr; }
};

int main()
{
    int n = 0;
    xreference_wrapper<int> ref(n); // 결국 주소보관

    int& r = ref; // ref.operator int&()

    r = 30;

    cout << n << endl; // 30
}
```

#### reference_wrapper

- 참조와 유사하게 동작하는 클래스 템플릿
- 참조로 변환 가능한 포인터

#### ref, cref

- reference_wrapper를 생성하는 helper 함수

---

#### chronometry 와 결합

``` cpp
#include <iostream>
using namespace std;

template<typename T> struct xreference_wrapper
{
    T* ptr;
public:
    xreference_wrapper(T& r) : ptr(&r) {}

    operator T&() { return *ptr; }
};

void foo(int& a) { a = 30; }

template<typename F, typename T>
void chronometry(F f, T arg)
{
    f(arg);
}

tempalte<typename T>
xreference_wrapper<T> xref(T& obj)
{
    return xreference_wrapper<T>(obj);
}

int main()
{
    int n = 0;
    // xreference_wrapper<int> r(n); // n 주소
    // chronometry(&foo, r);

    // chronometry(&foo, xreference_wrapper<int>(n));

    chronometry(&foo, xref(n));

    cout << n << endl; // 30
}
```

---

``` cpp
#include <iostream>
#include <functional>
using namespace std;

int main()
{
    int n1 = 10;
    int n2 = 20;

    // int& r1 = n1;
    // int& r2 = n2;

    reference_wratpper<int> r1(n1);
    reference_wratpper<int> r2(n2);

    r1 = r2;

    cout << n1 << endl; // 10
    cout << n2 << endl; // 20
    cout << r1 << endl; // 20
    cout << r2 << endl; // 20

    // vetor<int&> v(10); // reference_wrapper 는 보관할 수 있음
}
```
