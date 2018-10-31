---
layout: post
title:  "C++Intermediate&nbsp;10.&nbsp;C++기본문법_conversion#2"
date:   2018-10-31 04:18:15 +0700
categories: [c++]
---

codenuri 강석민 강사 강의 내용기반으로 정리한 내용입니다.

---

> return type resolver

```cpp
#include <iostream>
#include <cstdlib>
using namespace std;

int* memAlloc(int sz)
{
    return (int*)malloc(sz);
}

int main()
{
    double* p = memAlloc(40);
}
```

return 포인터를 double로 받고 싶을 때

템플릿 인자를 함수인자로 안쓰고 return 타입을 사용해 컴파일러가 추론할 수 없어 double로 전달해야함

```cpp
#include <iostream>
#include <cstdlib>
using namespace std;

template<typename T> T* memAlloc(int sz)
{
    return (int*)malloc(sz);
}

int main()
{
    // double* p = memAlloc(40);
    double* p = memAlloc<double>(40);
}
```

double을 적지 않고도 사용할 수 있도록

double* p1 = memAlloc(40);

int* p2 = memAlloc(40);

---

``` cpp
#include <iostream>
#include <cstdlib>
using namespace std;

/*
template<typename T> T* memAlloc(int sz)
{
    return (int*)malloc(sz);
}
*/
class memAlloc
{
    int size;
public:
    inline memAlloc(int sz) : size(sz) {}

    template<typename T> operator T*()
    {
        return (T*)malloc(size);
    }
};

int main()
{
    double* p1 = memAlloc(40); // 클래스 이름() : 임시객체..
    // 임시객체.operator double*() 을 찾을 것임
    int* p2 = memAlloc(40);
}
```

---

> safe bool

``` cpp
#include <iostream>
using namespace std;

int main()
{
    int n = 0;
    cin >> n; // 'a'

    if (cin.fail())
        cout << "실패" << endl;

    if (cin)
        cout << "성공" << endl;
}
```

if 문의 객체가 if문에 들어갈 수 있는 요소로 변환이 된다는 뜻

if(bool, 정수, 포인터, 실수) 만 올 수 있음

cin.operator void*() 로 변환이 되는 것임 -> C++98/03

cin.operator bool() -> C++11

옛날에는 void하고 왜 11로 와서 bool로 바뀌었는가?

``` cpp
class istream
{
public:
    bool fail() { return false;}

    // 방법 1. bool로 변환
    operator bool() {return fail() ? false : true;}
}

istream cin;

int main()
{
    int n = 0;

    if (cin) {}

    cin << n; // 사용자가 잘못적을 수 있음 -> 컴파일시 에러나와야하는데 컴파일은 잘됨

    // cin은 bool로 변환이되고 결국 정수라 쉬프트 연산이 가능해짐
}
```

bool로 변환 단점 : shift 연산이 허용된다.

bool로 변환하면 문제점이 크니까 void*로 변환하자

``` cpp
class istream
{
public:
    bool fail() { return false;}

    // 방법 2. void*로 변환
    operator void*() {return fail() ? 0 : this;}
}

istream cin;

int main()
{
    int n = 0;

    if (cin) {}

    cin << n;

    delete cin;
}
```

void*는 shift 연산 못하니까 error 발생

따라서 C++ 98/03은 if 문에 cin만 넣기위해 void*로 변환함

그러나 delete cin; 이 에러가 안남 -> this로 형변환 되기 때문에...

-> 부스트팀이 함수 포인터로 변환


``` cpp
void true_function() {}

class istream
{
public:
    bool fail() { return false;}

    // 방법 3. 함수 포인터로의 변환
    typedef void(*F)();
    operator F() {return fail() ? 0 : &true_function;}
}

istream cin;

int main()
{
    int n = 0;

    if (cin) {}

    cin << n;

    delete cin;
}
```

cin << n; delete cin; 모두에 error 발생해서 안전하게 사용가능함

그라나 void(*f)() = cin; 

cin이 함수포인터로 변환될 수 있다.

계속 사이드 이팩트 발생

결국 다시 멤버 함수 포인터로의 변환 


``` cpp
class istream
{
public:
    bool fail() { return false;}
    
    struct Dummy
    {
        void true_function() {}
    }

    // 방법 4. 멤버 함수 포인터로의 변환
    typedef void(Dummy::*F)();
    operator F() {return fail() ? 0 : &Dummy::true_function;}
}

istream cin;

int main()
{
    int n = 0;

    if (cin) {}

    cin << n;

    delete cin;

    void(*f)() = cin;
}
```

다시 void(Dummy::*f)() = cin; 이여야하는데 istream 안에 있으므로 error 

void(istream::Dummy::*f)() = cin; 이라고 일반 개발자가 적기 어려움

따라서 if 문에 넣을 수 있는 side effect 가 가장 적다.

-> Safe BOOL

if 문에는 넣을 수 있는데 잘못된 기법을 막는 방법으로 결국에는 멤버함수를 이용하는 것을 safe bool이라함

C++ 11에서 해결책 제시

> Explicit Conversion Operator
