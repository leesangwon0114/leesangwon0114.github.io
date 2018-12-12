---
layout: post
title:  "C++Intermediate&nbsp;33.&nbsp;Callable Object & invoke"
date:   2018-12-12 02:18:15 +0700
categories: [c++]
---

codenuri 강석민 강사 강의 내용기반으로 정리한 내용입니다.

---

> Callable Object & invoke

``` cpp
#include <iostream>
#include <functional>
using namespace std;

void foo()
{

}

int main()
{
    foo();

    less<int> f;
    f(1, 2);

    [](int a, int b) { return a + b; }(1, 2);
}
```

#### Function Object

- A Function Object type is the type of an object that can be used on the left of the function call operator(괄호의 왼쪽에 올 수 있는 Object)
- 모든 종류의 함수 포인터
- ()연산자를 재정의한 클래스
- Lambda Expression
- 진짜 함수와 함수를 가리키는 참조는 Function Object 가 아니다.

---

``` cpp
#include <iostream>
#include <functional>
using namespace std;

template<typename F, typename ... Types>
decltype(auto) chronometry(F&& f, Types&& ... args)
{
    return std::forward<F>(f)(std::forward<Types>(args)...);
}

int main()
{
    chronometry(less<int>(), 10, 20);
}
```

멤버함수를 보낼 때 문제됨

``` cpp
#include <iostream>
#include <functional>
using namespace std;

template<typename F, typename ... Types>
decltype(auto) chronometry(F&& f, Types&& ... args)
{
    // std::forward<F>(f)(std::forward<Types>(args)...);
    return invoke(std::forward<F>(f), std::forward<Types>(args)...); // 멤버함수 포인터 까지 호출 가능(C++17)
}

class Dialog
{
public:
    void Close() {}
};

int main()
{
    Dialog dlg;
    chronometry(&Dialog::Close, &dlg);

    void(Dialog::*f)() = &Dialog::Close;
    (dlg.*f)(); // 멤버함수 호출 원래 코드

    // f(&dlg); // 이렇게 호출하고 싶을 때
    invoke(f, &dlg); // invoke 사용
}
```

---

``` cpp
#include <iostream>
#include <functional>
using namespace std;

void foo(int a, int b) {}

class Dialog
{
public:
    int color;
    void setColor(int c) { color = c; }
};

int main()
{
    invoke(&foo, 10, 20); // foo(10, 20);

    Dialog dlg;
    invoke(&Dialog::setColor, &dlg, 2); // dlg.setColor(2);

    // 멤버 변수 포인터
    invoke(&Dialog::color, &dlg) = 20; // dlg.color = 20;
    cout << dlg.color << endl; // 20
}
```

#### Callable

- A Callable type is a type for which the INVOKE operation(used by, e.g, std::function, std::bind, and std::thread::thread) is applicable.
- Function Object
- Pointer to member
