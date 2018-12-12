---
layout: post
title:  "C++Intermediate&nbsp;32.&nbsp;Multi Interface Lambda"
date:   2018-12-12 02:18:15 +0700
categories: [c++]
---

codenuri 강석민 강사 강의 내용기반으로 정리한 내용입니다.

---

> Multi Interface Lambda

``` cpp
#include <iostream>
using namespace std;

int main()
{
    auto f = [](int a, int b) { return a + b; };

    f(1, 2);
    f(1, 2, 3);
    f(1, 2, 3, 4);
}
```

람다식에 가변인자 말고 Multi Interface를 갖는 Lambda를 만들어 보자

``` cpp
#include <iostream>
using namespace std;

template<typename ... Types> class mi : public Types...
{
public:
    mi(Types&& ... args) : Types(args)... {}

    // 기반 클래스의 특정함수를 사용할 수 있게..
    using Types::operator()...;
};

int main()
{
    mi f([](int a, int b) { return a + b; }, [](int a, int b, int c){ return a + b + c; } );

    cout << f(1, 2) << endl; // 3
    cout << f(1, 2, 3) << endl; // 6
}
```

람다는 default 생성자는 delete 되어 있지만 복사생성자는 default가 있음

기반 클래스의 특정함수를 사용할 수 있게 using 괄호연산자 사용
