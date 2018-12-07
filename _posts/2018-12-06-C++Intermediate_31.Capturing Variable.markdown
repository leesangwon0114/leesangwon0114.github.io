---
layout: post
title:  "C++Intermediate&nbsp;31.&nbsp;Capturing Variable"
date:   2018-12-06 02:18:15 +0700
categories: [c++]
---

codenuri 강석민 강사 강의 내용기반으로 정리한 내용입니다.

---

> Capture Variable #1

``` cpp
#include <iostream>
using namespace std;

int g = 10;

int main()
{
    int v1 = 0, v2 = 0;

    auto f1 = [](int a) { g=0; return a + g; }; // ok. 전역변수 접근 가능
                // class ClosureType{};

    auto f2 = [](int a) { return a + v1; }; // error. 지역변수 접근 불가능
    auto f3 = [v1](int a) { return a + v1; }; // ok. 지역변수 capture를 통해 접근 가능
    auto f4 = [v1, v2](int a) { return a + v1 + v2; }; // ok.

    auto f5 = [v1](int a) { v1 = 10; return a + v1; }; // error. 지역변수 변경은 안됨

    auto f6 = [v1](int a) mutable { v1 = 10; return a + v1; }; // ok. but 복사본이 변경되는 것임
}
```

---

``` cpp
#include <iostream>
using namespace std;

int main()
{
    int v1 = 0, v2 = 0;

    // capture
    auto f0 = []() { return 0; };
    auto f1 = [v1, v2]() { return v1 + v2; };

    // 람다표현식은 함수객체이므로 크기를 구할 수 있음

    cout << sizeof(f0) << endl; // 1
    cout << sizeof(f1) << endl; // 8 -> 지역변수 캡쳐 시 내부에 지역변수 생김
}
```

지역변수를 캡쳐했을 때 값을 변경할 수 있는지 여부를 살펴보자.

``` cpp
#include <iostream>
using namespace std;

int main()
{
    int v1 = 0, v2 = 0;
    auto f1 = [v1, v2]() { v1 = 10; v2 = 20 }; // error
    // 괄호 연산자가 상수 멤버함수 이므로 값을 변경할 수 없음

    // mutable lambda : () 연산자 함수를 비상수 함수로
    auto f2 = [v1, v2]() mutable { v1 = 10; v2 = 20 }; // 지역변수를 보내서 멤버 데이터에 옮긴 복사본임

    f2(); // 람다 표현식 실행...
    // 값을 변경했지만 여전히 0이 출력됨
    cout << v1 << endl; // 0
    cout << v2 << endl; // 0
}
```

reference 캡쳐를 통해 참조로 보낼 수 있음

``` cpp
#include <iostream>
using namespace std;

int main()
{
    int v1 = 0, v2 = 0;
    
    // capture by value, capture by copy
    auto f1 = [v1, v2]() mutable { v1 = 10; v2 = 20 };

    f1();

    cout << v1 << endl; // 0
    cout << v2 << endl; // 0

    // capture by reference
    auto f2 = [&v1, &v2]() { v1 = 10; v2 = 20 }; // mutable 뺌(참조가 가르키는 곳은 수정 가능)

    f2();

    cout << v1 << endl; // 10
    cout << v2 << endl; // 20
}
```

---

> Capture Variable #2

``` cpp
int main()
{
    int v1 = 0, v2 = 0, v3 = 0;

    auto f1 = [v1]() {};
    auto f2 = [&v1]() {};
    auto f3 = [v1, &v2]() {};

    // default capture
    auto f4 = [=]() {}; // capture by copy
    auto f5 = [=]() {}; // capture by reference
    auto f6 = [=, &v1]() {}; // 모든 데이터를 copy로 하는데 v1만 참조로 하겠다
    auto f7 = [&, v1] () {}; // 모든 데이터를 참조로 capture 할 건데 v1 만 값으로 하겠다.
    // auto f8 = [=, v1]() {}; // error

    auto f9 = [x=v1, v2=v2, v3] () {}; // 멤버데이터 x 에 v1 복사
    auto f10 = [v1, y=v2, &r=v3] () {};

    string s = "hello";
    auto f11 = [s1 = move(s)] () {};
    f11();
    cout << s << endl; // empty string

    unique_ptr<int> p (new int); // unique_ptr 은 복사 불가능 하니까 move로줌
    auto f12 = [p=move(p)]() {};
}

void foo(int a, int b)
{
    // auto f = []() {return a + b;}
    auto f = [a, b]() {return a + b;} // ok.
    auto f = [=]() {return a + b;} // ok.
}

template<typename ... Types> void goo(Types ... args)
{
    auto f1 = [=]() { auto t = make_tuple(args...); };
    auto f2 = [args...]() { auto t = make_tuple(args...); };
}
```

멤버데이터의 Capture

``` cpp
class Test
{
    int data = 0;
public:
    void foo() // Test* const this
    {
        int v = 0;
        // auto f = [this]() { this->data = 10; }; // this 이용
        auto f = [this]() { data = 10; }; // this 생략도 가능

        // 멤버 데이터를 복사본으로 캡쳐 - C++17
        auto f2 = [*this]() mutable { this->data = 10; };

        f();

        cout << data << endl; // 10
    }
};

int main()
{
    Test t;
    t.foo();
}
```

---

> Conversion

람다표현식과 함수포인터로의 변환

``` cpp
class ClosureType
{
public:
    int operator()(int a, int b) const
    {
        return a + b;
    }

    static int method(int a, int b)
    {
        return a + b;
    }
    
    
    typedef int(*F)(int, int);

    operator F()
    {
        // return &ClosureType::operator(); // 멤버함수는 불가 -> 일반함수 필요(static 하나 더 만듬)
        return &ClosureType::method;
    }
}

int main()
{
    int(*f)(int, int) = [](int a, int b)
    {
        return a + b;
    }
}
```

괄호연산자는 static 이 될 수 없으므로 별도의 static method 가 생성되어 리턴시킴

Capture 할 때 문제가 됨

``` cpp
class ClosureType
{
public:
    ClosureType(int a) : v(a) {}
    int operator()(int a, int b) const
    {
        return a + b + v;
    }

    static int method(int a, int b)
    {
        return a + b + v; ? error
    }
    
    
    typedef int(*F)(int, int);

    operator F()
    {
        // return &ClosureType::operator(); // 멤버함수는 불가 -> 일반함수 필요(static 하나 더 만듬)
        return &ClosureType::method;
    }
}

int main()
{
    int(*f)(int, int) = [](int a, int b)
    {
        return a + b;
    }

    int v = 0;

    // capture 구문을 사용하면 함수포인터로 암시적 변환 될 수 없다.
    int(*f)(int, int) = [v](int a, int b)
    {
        return a + b + v;
    }
}
```

따라서 Capture 하지 않은 함수포인터만 암시적 변환이 됨

---

> Lambda MISC

``` cpp
#include <iostream>
using namespace std;

// g++ 만 지원되고 있음(Concept Ts 내용)
// void foo(auto n) {}

int main()
{
    // generic lambda : C++14
    auto f1 = [](auto a, auto b) { return a + b; };
    cout << f1(1, 2.1) << endl;

    auto f2 = [](int a) { return 10; };
    // nullary lambda
    auto f4 = [] { return 10; }; // 인자가 없을 때 () 빼도됨

    // C++17 : () 함수를 constexpr 함수로...(인자로 liternal이 오면 컴파일 시간에 대입시킴)
    auto f3 = [](int a, int b) constexpr
    {
        return a + b;
    };

    int y[f3(1,2)]; // 컴파일 시간에 계산되면 가능
}
```

---

``` cpp
#include <iostream>
using namespace std;

int main()
{
    auto f1 = [](int a, int b) { return a + b; };

    decltype(f1) f2; // error. default 생성자가 삭제되어 있어서 error

    decltype(f1) f3 = f1; // ok. 복사생성자는 기본버전이 제공되 문제 없음

    decltype(f1) f4 = move(f1); // ok. Move 생성자도 기본버전이 제공됨
}
```
