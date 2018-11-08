---
layout: post
title:  "C++Intermediate&nbsp;14.&nbsp;C++17기본문법"
date:   2018-11-08 04:18:15 +0700
categories: [c++]
---

codenuri 강석민 강사 강의 내용기반으로 정리한 내용입니다.

---

> if-init, if constexpr

```cpp
#include <iostream>
using namespace std;

int foo()
{
    return 0;
}

int main()
{
    /*
    int ret = foo();

    if(ret == 0)
    {

    }
    */
    // if (init 구문; 조건문)
    if (int ret = foo(); ret == 0)
    {
        cout << "ret is 0" << endl;
    }

    switch (int n = foo(); n)
    {
    case 0:  break;
    case 1:  break;
    }
}
```

---

``` cpp
#include <iostream>
#include <type_traits>
using namespace std;

template<typename T> void printv(T v)
{
    if (is_pointer<T>::value) // if (false)
        cout << v << " : " << *v << endl;
    else
        cout << v << endl;
}

int main()
{
    int n = 10;
    printv(n);
    printv(&n);
}
```

빌드시 에러 -> *v 값 타임일 때는 불가능

if 자체가 실행시간 메커니즘

컴파일 시간에 if 문을 조사하는 static-if 사용

``` cpp
#include <iostream>
#include <type_traits>
using namespace std;

template<typename T> void printv(T v)
{
    // static - if
    if constexpr (is_pointer<T>::value)
        cout << v << " : " << *v << endl;
    else
        cout << v << endl;
}

int main()
{
    int n = 10;
    printv(n);
    printv(&n);
}
```

if constexpr 은 컴파일 시간의 if 이므로 if constexpr(v==10) 같은 변수를 사용하는 표현식은 넣을 수 없고 컴파일 시간에 결정되는 표현만 넣을 수 있음

---

> structure binding

``` cpp
#include <iostream>
#include <tuple>
using namespace std;

struct Point
{
    int x;
    int y;
};

int main()
{
    Point pt = {1,2};
    // int a = pt.x;
    // int b = pt.y;

    auto [a ,b] = pt;
    auto& [rx, ry] = pt;

    int x[2] = {1,2};
    auto[e1, e2] = x;

    pair<int, double> p (1, 3.4);
    auto[n, d] = p;

    tuple<int, short, double> t3(1, 2, 3.4);
    auto[a1, a2, a3] = t3;
}
```

``` cpp
#include <iostream>
#include <set>
using namespace std;

int main()
{
    set<int> s;

    s.insert(10);
    // pair<set<int>::iterator, bool> ret = s.insert(10); // 실패

    /*
    auto ret = s.insert(10);

    if (ret.second == false)
    {
        cout << "실패" << endl;
    }
    */
    /*
    auto [it, success] = s.insert(10);

    if(ret.second == false)
    {
        cout << "실패" << endl;
    }
    */

    if(auto [it, success] = s.insert(10); success == false)
    {
        cout << "실패" << endl;
    }
}
```
