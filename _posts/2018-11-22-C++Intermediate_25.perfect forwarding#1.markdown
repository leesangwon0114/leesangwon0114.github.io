---
layout: post
title:  "C++Intermediate&nbsp;25.&nbsp;perfect forwarding#1"
date:   2018-11-22 02:18:15 +0700
categories: [c++]
---

codenuri 강석민 강사 강의 내용기반으로 정리한 내용입니다.

---

> Perfect Forwarding 개념

``` cpp
#include <iostream>

using namespace std;

void goo(int& a) { cout << "goo" << endl; a = 30; }

void foo(int a) { cout << "foo" << endl; }

// 완벽한 전달 : 래퍼함수가 인자를 받아서 원본 함수에게 완벽하게 전달하는 개념
// perfect forwarding.
template<typename F, typename A>
void chronometry(F f, A& arg)
{
    f(arg);
}

int main()
{
    int n = 0;
    // goo(n);
    // foo(5);
    chronometry(&goo, n); // goo(n)
    // chronometry(&foo, 5); // foo(5) 실행 시 수행시간

    cout << n << endl; // 30
}
```

---

> Perfect Forwarding

핵심 1. 함수 인자를 받을 때 reference 를 사용해야 한다.

핵심 2. lvalue reference 와 rvalue reference 버전 모두 제공해야한다.

``` cpp
#include <iostream>

using namespace std;

void goo(int& a) { cout << "goo" << endl; a = 30; }

void foo(int a) { cout << "foo" << endl; }

template<typename F>
void chronometry(F f, int& arg)
{
    f(arg);
}

template<typename F>
void chronometry(F f, int&& arg)
{
    f(arg);
}

int main()
{
    int n = 0;

    chronometry(&goo, n);
    chronometry(&foo, 5);

    cout << n << endl;
}
```

완벽한 전달을 하려면

1. 함수 인자를 받을 때 reference 를 사용해야 한다.
2. lvalue reference 버전과 rvalue reference 버전을 모두 제공해야 한다.
3. rvalue reference 버전의 함수에서는 인자를 원본 함수에 전달할 때 rvalue로 캐스팅해서 전달한다.

``` cpp
#include <iostream>

using namespace std;

void goo(int& a) { cout << "goo" << endl; a = 30; }

void foo(int a) { cout << "foo" << endl; }

void hoo(int&& a) { cout << "hoo" << endl; }

template<typename F>
void chronometry(F f, int& arg)
{
    f(arg);
}

template<typename F>
void chronometry(F f, int&& arg)
{
    // f(arg);
    // 해결책 : lvalue인 arg를 rvalue로 다시 캐스팅 한다
    f(static_cast<int&&>(arg));
}

int main()
{
    chronometry(&hoo, 10); // error -> static_cast

    int&& arg = 10; // 10은 rvalue, arg는 lvalue 이다.

    int n = 0;

    chronometry(&goo, n);
    chronometry(&foo, 5);

    cout << n << endl;
}
```

---

> forwarding reference

모든 타입에 적용할 수 있도록 변경

#### forwarding reference = 함수 인자로 T&&를 사용하는 경우
1. lvalue 와 rvalue 를 모두 전달 받을 수 있다.
2. 인자로 lvalue 전달 시 -> T: int&, T&&: int& && => int&
3. 인자로 rvalue 전달 시 -> T: int, T&&: int&& 

forwarding reference 만 만들면 lvalue, rvalue 모두생성할 필요 없이 컴파일러가 만들어줌

``` cpp
#include <iostream>

using namespace std;

void goo(int& a) { cout << "goo" << endl; a = 30; }

void foo(int a) { cout << "foo" << endl; }

void hoo(int&& a) { cout << "hoo" << endl; }

// T& : 모든 타입 lvalue
// T&& : 모든 타입 lvalue 와 rvalue 모두 받는다.
// forwarding reference

template<typename F, typename T>
void chronometry(F f, T&& arg)
{
    // f(static_cast<T&&>(arg));
    f(std::forward<T>(arg)); // forward()가 결국 내부적으로 위의 캐스팅 수행
}

int main()
{
    int n = 0;
    chronometry(&hoo, n);
    chronometry(&goo, n); // lvalue 버전 함수 생성
    chronometry(&foo, 5); // rvalue 버전 함수 생성

    cout << n << endl;
}
```

#### 완벽한 전달을 사용하는 함수를 만드는 방법
1. 함수 인자로 forwarding reference 를 사용한다
2. 함수 인자를 원본 함수에 보낼 때 std::forward<T>(arg)로 묶어서 전달한다

---

> chronometry

