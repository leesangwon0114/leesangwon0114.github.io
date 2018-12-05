---
layout: post
title:  "C++Intermediate&nbsp;30.&nbsp;Lambda Expression & Closure Object"
date:   2018-12-05 02:18:15 +0700
categories: [c++]
---

codenuri 강석민 강사 강의 내용기반으로 정리한 내용입니다.

---

> Closure Object

C++11 부터 추가된 람다 표현식

``` cpp
#include <iostream>
#include <algorithm>
#include <functional>
using namespace std;

bool cmp(int a, int b) { return a < b; }

int main()
{
    int x[10] = {1,3,5,7,9,2,4,6,8,10};

    // C++ 11. 람다 표현식(lambda expression)
    sort(x, x+10, [](int a, int b) { return a < b; });
}
```

#### Lambda Expression

[] int(a, int b) { return a < b; }

[] : lambda - introducer, 람다 표현식이 시작 됨을 나타냄

---

람다표현식에 의해 만들어지는 임시객체를 Closure라 부르고 해당 타입을 ClosureType이라 부름

``` cpp
#include <iostream>
#include <algorithm>
#include <functional>
using namespace std;

class ClosureType
{
public:
    bool operator() (int a, int b) const
    {
        return a < b;
    }
};

sort(x, x+10, ClosureType());

int main()
{
    int x[10] = {1,3,5,7,9,2,4,6,8,10};

    // C++ 11. 람다 표현식(lambda expression)
    sort(x, x+10, [](int a, int b) { return a < b; });

    bool b = [](int a, int b) { return a < b; } (1, 2);
    // class ClosureType{}; ClosureType()(1, 2);
    cout << b << endl; // 1
}
```

실제로는 복사생성자, Move 생성자, 함수포인터 지원 등이 추가됨

---

> lambda & type

#### lambda expression 활용 1. 함수 인자로 사용

#### lambda expression 활용 2. auto 변수에 담아서 함수처럼 사용

``` cpp
#include <iostream>
#include <algorithm>
using namespace std;

class ClosureType
{
public:
    int operator() (int a, int b) const
    {
        return a + b;
    }
};
ClosureType();

int main()
{
    // 활용 1
    sort(x, x+10, [](int a, int b) { return a < b; });

    // [](int a, int b) { return a + b; }; // 임시객체생성
    // int n = [](int a, int b) { return a + b; }(1, 2); 

    // 활용 2
    auto f = [](int a, int b) { return a + b; };
    cout << f(1, 2) << endl;
}
```

#### Lambda Expression 활용

1. 함수 인자로 전달
2. auto 변수에 담아서 함수 처럼 사용
3. 함수 리턴 값 등으로 활용가능(고차함수)

---

람다표현식과 타입

``` cpp
#include <iostream>
#include <typeinfo>
using namespace std;

int main()
{
    auto f1 = [](int a, int b) {return a + b;};
    cout << type(f1).name() << endl;

    auto f2 = [](int a, int b) {return a + b;};

    // f2 = [](int a, int b) {return a + b;}; // error. 서로 다른 타입은 대입될 수 없음, 컴파일러가 대입연산을 delete 하기도 함

    cout << type(f2).name() << endl;
}
```

두개의 다른 결과가 출력됨

-> 람다표현식은 같은 표현이라도 전부 다른 타입임

모든 lambda expression은 서로 다른 타입입니다.

---

> lambda & function argument

#### Lambda Expression 을 담는 방법

1. auto 변수 사용 - inline 가능성이 높다.
2. 함수포인터 - inline 치환이 어렵다.
3. std::function - inline 치환이 어렵다.

``` cpp
#include <iostream>
#include <functional>
using namespace std;

int main()
{
    auto f1 = [](int a, int b) { return a + b; };

    int(*f2)(int ,int) = [](int a, int b) { return a + b; };

    // f2 = [](int a, int b) { return a - b; };

    function<int(int, int)> f3 = [](int a, int b) { return a + b; };

    f1(1,2); // inline 치환 가능.
    f2(1,2); // inline 치환이 어렵다.
    f3(1,2); // inline 치환이 어렵다.
}
```

---

#### Lambda Expression 을 함수 인자로 받는 방법

1. 함수 포인터, function -> inline 치환이 어렵다.
2. template
3. 임시객체 이므로 non-const lvalue reference로 받을 수 없다.

``` cpp
#include <iostream>
#include <functional>
using namespace std;

// 람다표현식의 타입을 지우는 개념으로 inline 치환 불가
// void foo(int(*f)(int, int))
// void foo(function<int(int,int) f)
// void foo(auto f) // auto는 함수 인자가 될 수 없다. -> 컴파일 시 에러

// 모두 inline 치환 되게 하려면 foo 함수가 두개 만들어 져야함 -> template
template<typename T> void foo(T f)
{
    f(1,2);
}

int main()
{
    foo([](int a, int b) { return a + b; });
    foo([](int a, int b) { return a - b; });
}
```

---

> lambda & suffix return type

