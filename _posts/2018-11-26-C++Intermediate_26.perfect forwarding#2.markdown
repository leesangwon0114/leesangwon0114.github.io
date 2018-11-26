---
layout: post
title:  "C++Intermediate&nbsp;26.&nbsp;perfect forwarding#2"
date:   2018-11-26 02:18:15 +0700
categories: [c++]
---

codenuri 강석민 강사 강의 내용기반으로 정리한 내용입니다.

---

> Using Perfect Forwarding

#### Vector

``` cpp
#include <iostream>
#include <vector>
using namespace std;

class Point
{
    int x, y;
public:
    Point(int a, int b) { cout << "Point()" << endl; }
    ~Point() { cout << "~Point()" << endl; }
    Point(const Point&)
    {
        cout << "Point(const Point&)" << endl;
    }
};

int main()
{
    vector<Point> v;

    Point p(1,2);
    v.push_back(p);

    // 소멸자 호출 횟수: 2
}
```

Point 를 만들어서 복사하지말고 그냥 Point 1, 2를 전달 받아 객체를 만들기 위한 생성자 인자를 전달하자 -> emplace_back

``` cpp
#include <iostream>
#include <vector>
using namespace std;

class Point
{
    int x, y;
public:
    Point(int a, int b) { cout << "Point()" << endl; }
    ~Point() { cout << "~Point()" << endl; }
    Point(const Point&)
    {
        cout << "Point(const Point&)" << endl;
    }
};

int main()
{
    vector<Point> v;
    v.emplace_back(1,2);

    // 소멸자 호출 횟수: 1
}
```

---

#### shared_ptr

객체와 관리객체를 따로 잡지 말고 객체와 관리객체 한번에 할당하자 -> make_shared

``` cpp
#include <iostream>
#include <memory>
using namespace std;

class Point
{
    int x, y;
public:
    Point(int a, int b) { cout << "Point()" << endl; }
    ~Point() { cout << "~Point()" << endl; }
    Point(const Point&)
    {
        cout << "Point(const Point&)" << endl;
    }
};

int main()
{
    // 메모리 할당은 몇번 될까?
    shared_ptr<Point> sp(new Point(1,2)); // 객체와 관리객체를 따로 잡아 두번 할당
    shared_ptr<Point> sp2 = make_shared<Point>(1,2); // 한번에 객체와 관리객체 한번에 할당 -> (1,2)를 완벽 전달하기위해 perfect forwarding 사용함
}
```

---

> std:forward #1

``` cpp
void goo(int& a) { cout << "goo" << endl;}
void hoo(int&& a) { cout << "hoo" << endl; }

template<typename F, typename T>
void chronometry(F f, T&& arg)
{
    f(std::forward<T>(arg));
}

int main()
{
    int n = 0;
    chronometry(&goo, n);
    chronometry(&hoo, 1);
    cout << n << endl;
}
```

n: arg:lvalue, T: int&

1: arg:lvalue, T: int

arg는 둘다 lavlue라 std::forward(arg); 일 때는 두 개 값이 같아서 전단계 인자가 무엇인지 알 수 없음

근데 T는 전단계 인자 구분가능하여 반드시 <T> 정보를 주어야함

#### std::forward

lvalue를 인자로 받아서 T의 타입에 따라 lvalue 또는 rvalue로 캐스팅 한다.

---

4분 28초 구현

#### forward 구현하기

``` cpp
void goo(int& a) { cout << "goo" << endl;}
void hoo(int&& a) { cout << "hoo" << endl; }

template<typename F, typename T>
void chronometry(F f, T&& arg)
{
    f(std::forward<T>(arg));
}

int main()
{
    int n = 0;
    chronometry(&goo, n);
    chronometry(&hoo, 1);
    cout << n << endl;
}
```