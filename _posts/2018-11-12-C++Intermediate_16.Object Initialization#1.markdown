---
layout: post
title:  "C++Intermediate&nbsp;16.&nbsp;Object Initialization#1"
date:   2018-11-12 04:18:15 +0700
categories: [c++]
---

codenuri 강석민 강사 강의 내용기반으로 정리한 내용입니다.

---

> 초기화 코드의 문제점

#### C++98/03 초기화 구문의 문제점

1. 객체의 종류에 따라 초기화 방법이 다르다
2. 클래스의 멤버로 있는 배열을 초기화 할 수 없다.
3. 동적 메모리 할당으로 만들어진 배열을 초기화 할 수 없다.
4. STL 컨테이너를 초기화하는 편리한 방법이 없다.

```cpp
#include <iostream>
using namespace std;

int main()
{
    // 1.
    int n1 = 0;
    int n2(0);
    int ar[3] = {1,2,3};
    Point p = {1,2}; // 구조체
    complex c(1,2); // 클래스

    // 2.
    class Test
    {
        int x[10];
    };

    // 3.
    int* p = new int[3];

    // 4.
    vector<int> v;
    for (int i = 0; i < 10; i++)
        v.push_back(1);
}
```

---

``` cpp
#include <iostream>
using namespace std;

int cnt = 0;

class Test
{
public:
    // int data = 0; // member initializer
    int data = ++cnt;

    Test() {}
    Test(int n) : data(n) {}
};

int main()
{
    Test t1; // data = 1
    Test t2(3); // data = 3

    cout << cnt << endl; // 초기화리스트 코드가 있으면 ++cnt 는 안됨 -> 1

    cout << t1.data << endl;
    cout << t2.data << endl;
}

```

---

> Uniform Initialization

#### 일관된 초기화
1. 객체의 형태에 상관 없이 중괄호({}) 를 사용해서 초기화 하는 것

``` cpp
struct Point
{
    int x, y;
};

class Complex
{
    int re, im;
public:
    Complex(int r, int i) : re(r), im(i) {}
};

int main()
{
    /*
    int n = 0;
    int x[2] = {1, 2};
    Point p = {1, 2};
    Complex c(1,2);
    */

    int n {0};
    int x[2] {1, 2};
    Point p {1, 2};
    Complex c {1, 2};

    int n2 = 3.4; // ok
    // int n3 = {3.4}; // error

    // char c1{300}; // error
    char c2{100}; // ok -> 100은 1바이트 안에 들어가므로
}
```

---

> direct vs copy

#### direct initialization

초기화 시에 =을 사용하지 않는것

#### copy initialization

초기화 시에 =을 사용하는 초기화

``` cpp
int main()
{
    int n1 = 0; // copy initialization
    int n2(0); // direct initialization

    int n3 = {0};
    int n4{0};
}
```

---

이제 인자가여러개 되어도 explicit 붙일 수 있음

``` cpp
class Point
{
    int x, y;
public:
    // explicit: 변환 생성자로 사용될 수 없다.
    //           copy initialization 될 수 없다.
    explicit Point() : x(0), y(0) {}
    explicit Point(int a) : x(a), y(0) {}
    explicit Point(int a, int b) : x(a), y(b) {}
};

int main()
{
    Point p1(5); // ok.
    Point p2 = 5; // ok. 하지만 생성자 explicit 라면 error

    Point p3(1,1); // ok
    Point p4 = (1,1); // error

    Point p5{1,1}; // ok
    Point p6 = {1,1}; // ok 하지만 생성자 explicit 라면 error

    Point p7;
    Point p8{}; // direct
    Point p9 = {} // copy
}
```

---

> default vs value initialization

``` cpp
int main()
{
    // direct initialization
    int n1(0);
    int n2 {0};

    // copy initialization
    int n3 = 0;
    int n4 = {0};

    // --------------------------------

    int n5; // default initialization. 쓰레기 값
    int n6{}; // value initialization. 0으로 초기화

    cout << n5 << endl;
    cout << n6 << endl;

    int n7(); // 주의. 함수 선언
}
```

#### default initialization

초기화 구문 없이 초기화 하는 것

#### value initialization

빈 초기화 구문을 가지고 초기화 하는 것

---

#### 사용자 정의 타입과 default / value 초기화

``` cpp
#include <iostream>
using namespace std;

class Point
{
public:
    int x;
    int y;

    Point() {}
};

int main()
{
    Point p1; // default initialization
    Point p2{}; // value initialization

    cout << p1.x << endl; // 쓰레기 값
    cout << p2.x << endl; // 0이 되야하는데 사용자 정의 초기화가 있으면 쓰레기 값 나옴
}
```

``` cpp
#include <iostream>
using namespace std;

class Point
{
public:
    int x;
    int y;

    Point() = default
};

int main()
{
    Point p1; // default initialization
    Point p2{}; // value initialization

    cout << p1.x << endl; // 쓰레기 값
    cout << p2.x << endl; // default 면 0으로 초기화됨
}
```

---

``` cpp
#include <iostream>
using namespace std;

int main()
{
    int n1; // default 쓰레기 값
    int n2{}; // value 0
    int n3(); // 함수 선언

    int* p1 = new int; // default 쓰레기 값
    int* p2 = new int(); // value 0
    int* p3 = new int{}; // value 0

    cout << *p1 << endl; // 쓰레기 값
    cout << *p2 << endl; // 0
    cout << *p3 << endl; // 0
}
```
