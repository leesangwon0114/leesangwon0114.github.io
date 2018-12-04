---
layout: post
title:  "C++Intermediate&nbsp;29.&nbsp;function object"
date:   2018-11-29 02:18:15 +0700
categories: [c++]
---

codenuri 강석민 강사 강의 내용기반으로 정리한 내용입니다.

---

> Functor

``` cpp
#include <iostream>
using namespace std;

// Function Object(functor)
class Plus
{
public:
    int operator()(int a, int b)
    {
         return a + b;
    }
};

template <typnename T> struct Plus 
{
    T operator()(T a, T b) const
    {
         return a + b;
    }
};

void foo(const Plus& p)
{
     int n = p(1, 2);
}

int main()
{
    Plus p; // plus 타입의 객체

    int n = p(1, 2); // p.operator()(1,2)

    cout << n << endl;
}
```

a + b : a.operator+(b)

a - b : a.operator-(b)

a(); : a.operator()() - 앞에 괄호는 연산자 괄호, 뒤의 괄호는 호출 괄호

a(1, 2); : a.operator()(1,2)

함수객체를 간단히 만들 때는 클래스 보다 구조체 이용

인자로 넘길 때 상수 참조를 사용할 수 있게 const 를 붙임

---

> Functor 장점

``` cpp
#include<algorithm>
#include<iostream>
using namespace std;

// 변하지 않은 전체 흐름속에서.. 변경되어야 하는 부분을 분리한다.
// qsort() 함수와 유사한 방법

// 일반함수는 자신만의 타입이 없다.
// signature가 동일하면 모두 같은 타입이다.
void Sort(int* x, int n, bool(*cmp)(int, int))
{
    for (int i = 0; i < n - 1; i++)
    {
        for(int j = i + 1; j < n; j++)
        {
            // if (x[i] > x[j])
            if(cmp(x[i], x[j]))
                swap(x[i], x[j]);
        }
    }
}

inline bool cmp1(int a, int b) { return a > b; }
inline bool cmp2(int a, int b) { return a < b; }

int main()
{
    int x[10] = {1,3,5,7,9,2,3,6,8,10};
    Sort(x, 10);
}
```

함수 포인터로 되어 있어 일반함수는 인자로 넘어올 수 있는 함수가 너무 많아 인라인 치환이 불가(컴파일러가 어느함수가 불릴지 모름)

함수객체를 이용해보자!

``` cpp
#include<algorithm>
#include<iostream>
using namespace std;

// 함수 객체는 자신만의 타입이 있다.
// signature가 동일 해도 모든 함수객체는 다른 타입이다.
struct Less
{
    inline bool operator() (int a, int b) const { return a < b; }
};

struct Greater
{
    inline bool operator() (int a, int b) const { return a > b; }
};

// void Sort(int* x, int n, bool(*cmp)(int, int))
// void Sort(int* x, int n, Less cmp)

// 정책 변경가능하고 정책이 인라인 치환되는 함수. (템플릿 + 함수객체)
template<typename T> void Sort(int* x, int n, T cmp)
{
    for (int i = 0; i < n - 1; i++)
    {
        for(int j = i + 1; j < n; j++)
        {
            if(cmp(x[i], x[j]))
                swap(x[i], x[j]);
        }
    }
}

int main()
{
    Less f1; // 타입 Less
    Greater f2; // 타입 Greater
    int x[10] = {1,3,5,7,9,2,3,6,8,10};
    Sort(x, 10, f1);
    Sort(x, 10, f2);
}
```

단점 : 템플릿이니까 T타입이 결정될 때 함수 생성(Less 버전과 Greater 버전 두개의 sort 함수가 생김) -> 목적코드가 커지는 단점(Sort 가 목적코드가 크지 않으므로 성능항상을 위해 이 방식이 좋을 수 있음)

---

> 함수 객체 vs 일반함수

``` cpp
#include<algorithm>
#include<iostream>
using namespace std;

// 일반함수
inline bool cmp1(int a, int b) { return a > b; }
inline bool cmp2(int a, int b) { return a < b; }

// functor
struct Less
{
    inline bool operator() (int a, int b) const { return a < b; }
};

struct Greater
{
    inline bool operator() (int a, int b) const { return a > b; }
};

int main()
{
    int x[10] = {1,3,5,7,9,2,3,6,8,10};
    // STL sort : 모든 인자가 템플릿으로 되어 있다.
    sort(x, x+10, cmp1); // sort(int*, int*, bool(*)(int, int)) 인 함수 생성.
    sort(x, x+10, cmp2); // sort(int*, int*, bool(*)(int, int)) 인 함수 생성.

    Less f1;
    Greater f2;
    sort(x, x+10, f1); // sort(int*, int*, Less) 인 함수 생성
    sort(x, x+10, f2); // sort(int*, int*, Greater) 인 함수 생성
}
```

#### 비교정책으로 일반함수를 전달할 때.

장점 : 정책을 교체해도 sort() 기계어는 한개이다. - 코드 메모리 절약

단점 : 정책 함수가 인라인 치환될수 없다.(데이터 양 많을 때 성능저하가 큼)

#### 비교정책으로 함수객체 전달할 때.

장점 : 정책함수가 인라인 치환될 수 있다. - 빠르다.!

단점 : 정책을 교체한 횟수 만큼의 sort() 기계어 생성.

-> 상황에 따라 일반함수, 함수객체 선택해야함.

---

STL에 이미 함수객체를 쓰도록 준비해주고 있음.

``` cpp
#include<algorithm>
#include<iostream>
#include<functional> // less<>, greater<>
using namespace std;

int main()
{
    int x[10] = {1,3,5,7,9,2,3,6,8,10};
    less<int> f1;
    sort(x, x+10, f1);

    sort(x, x+10, less<int>());
}
```

인라인 치환성 말고 상태를 가지는 또 다른 장점이 뒤에 설명됨.

---

> 상태를 가지는 함수

``` cpp
#include <iostream>
#include <string>
#include <bitset>
using namespace std;

int main()
{
    bitset<10> bs;

    bs.reset(); // 모든 요소를 0
    bs.reset(4); // 4번째 비트를 0으로

    bs[2] = 1;
    bs[1].flip(); // 뒤집어 달라는 것 -> 첫번째가 1
    // 000000110

    string s = bs.to_string();
    unsigned long n = bs.to_ulong();

    cout << s << endl; // 000000110
    cout << n << endl; // 6
}
```

---

``` cpp
#include <iostream>
#include <bitset>
using namespace std;

// 0 ~ 9 사이의 중복되지 않은 난수를 리턴하는 함수.
int random()
{
    int v = -1;
    static bitset<10> bs; // 10개가 0으로 초기화
    do
    {
        v = rand() % 10;
    } while(bs.test(v));

    bs.set(v);
    return rand() % 10;
}

int main()
{
     cout << random() << " "; 
     random(); // 무한루프에 빠짐...
}
```

전역변수로 뽑음

``` cpp
#include <iostream>
#include <bitset>
using namespace std;

bitset<10> bs; // 10개가 0으로 초기화
bitset<10> bs1;
void clear_random() { bs.reset(); }

// 0 ~ 9 사이의 중복되지 않은 난수를 리턴하는 함수.
int random()
{
    int v = -1;
    do
    {
        v = rand() % 10;
    } while(bs.test(v));

    bs.set(v);
    return rand() % 10;
}

int main()
{
     cout << random() << " "; 
     random(); // 무한루프에 빠짐...
}
```

C의 함수는 동작은 있지만 상태를 보관할 수 없음

상태를 관리하기위해 전역변수를 쓰다보니까 상태가 여러개 필요하면 2 ~ 3개 이상 필요...

함수객체로 해결

``` cpp
#include <iostream>
#include <bitset>
using namespace std;

class Random
{
    bitset<10> bs;
public:
    Random()
    {
        bs.reset(); // 모든 비트를 0으로
    }
    int operator()()
    {
        int v = -1;
        do
        {
            v = rand() % 10;
        } while(bs.test(v));

        bs.set(v);
        return rand() % 10;
    }
};

int main()
{
    Random random;
    cout << random() << " ";

    // 상태하나 더있으려면 객체하나 더 만들면됨
    Random random1;
    random1();
}
```

함수객체는 class 이므로 동작과 상태를 가질 수 있음

상태를 초기화 할 수 있는 생성자, 소멸자 등 도 있음

---

> 함수 객체 요점정리

