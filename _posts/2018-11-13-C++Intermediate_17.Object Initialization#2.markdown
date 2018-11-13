---
layout: post
title:  "C++Intermediate&nbsp;17.&nbsp;Object Initialization#2"
date:   2018-11-13 04:18:15 +0700
categories: [c++]
---

codenuri 강석민 강사 강의 내용기반으로 정리한 내용입니다.

---

> initializer_list

```cpp
#include <iostream>
#include <initializer_list>
using namespace std;

int main()
{
    // brace-init-list
    initializer_list<int> s = {1,2,3,4,5};

    auto p = begin(s); // 상수를 가리키는 반복자
    // *p = 20; // error

    cout << *p << endl;
}
```

#### Initializer_list

- brach-init-list 로 초기화된 요소의 시작과 끝을 가리키는 클래스
- 반복자는 상수를 가리킨다.

---

Initializer_list를 함수의 인자로 사용하는 방법

``` cpp
#include <iostream>
#include <initializer_list>
using namespace std;

void foo(initializer_list<int> e)
{
    auto p1 = begin(e);
    auto p2 = end(e);

    for (auto n : e)
        cout << n << " ";
    cout << endl;
}

int main()
{
    initializer_list<int> e1 = {1,2,3};
    initializer_list<int> e2 = {1,2,3,4,5};

    foo(e1);
    foo({1,2,3,4});
}
```

---

Initializer_list를 클래스의 생성자 인자로 사용하는 경우

``` cpp
class Point
{
    int x, y;
public:
    Point(int a, int b) { cout << "int, int" << endl; } // 1
    Point(initializer_list<int>) { cout << "initializer_list<int>" << endl; } // 2
};

int main()
{
    Point p1(1,1); // 1번 없다면 error

    Point p2({1,1}); // 2번, 만일 1번 없다면 {1,1} 이 변환 생성자를 사용해서 임시객체 생성, 복사생성자를 사용해서 p2 복사 -> 1번에 explicit 붙이면 error 발생

    Point p3{1,1}; // 2. 없으면 1번

    Point p4 = {1,1}; // 2. 없으면 1번, explicit 면 error

    Point p5(1,2,3); // error

    Point p6{1,2,3}; // 2번

    Point p7 = {1,2,3};
}

```

#### Point p2({1,1});

- {1,1} 변환 생성자를 사용해서 임시객체 생성. 복사생성자를 사용해서 p2 복사
- 없으면 error 지만, explicit 가 아닐 경우 변환생성

#### Point p3{1,1};

- 2번이 불림
- 2번이 없으면 1번 부름

---

``` cpp
#include <iostream>
#include <vector>
using namespace std;

template<typename T, typename Ax = allocator<T>> class vector
{
    T* buff;
public:
    vector(size_t sz, T v = T()) {} // 1
    vector<initializer_list<T> s) {} // 2
};

int main()
{
    vector<int> v = {1,2,3,4,5}; // ok. 편리해 졌다
    vector<int> v1(10, 3); // 1번. 10개를 3으로 초기화
    vector<int> v2{10, 3}; // 2번, 2개의 요소를 10, 3으로 초기화

    cout << v1.size() << endl; // 10
    cout << v2.size() << endl; // 2
}
```

---

> aggregate initialization

``` cpp
struct Point
{
    int x, y;
    
    Point() {} // 1
    Point(int a, int b) {} // 2
};

int main()
{
    Point p1; // 1
    Point p2 { 1, 2 }; // 2
}
```

``` cpp
struct Point
{
    int x, y;
    
    // Point() {} // 1
    // Point(int a, int b) {} // 2
};

int main()
{
    Point p1; // ok. 컴파일러가 제공하는 생성자로 가능
    Point p2 { 1, 2 }; // ok. 구조체는 생성자 없더라도 Point p2 = {1,2} 도 되므로 똑같음
}
```

생성자가 없더라도 중괄호로 초기화 가능하면 aggregate initialization이라함

``` cpp
struct Point
{
    int x, y;
    
    Point() {} // 1
    // Point(int a, int b) {} // 2
};

int main()
{
    Point p1; // ok. 컴파일러가 제공하는 생성자로 가능
    Point p2 { 1, 2 }; // error
}
```

``` cpp
struct Point
{
    int x, y;
    
    Point() = default;
    // Point() {} // 1
    // Point(int a, int b) {} // 2
};

int main()
{
    Point p1; // ok
    Point p2 { 1, 2 }; // ok
}
```

``` cpp
struct Point
{
    int x, y;
    
    virtual void foo() {}
    // Point() = default;
    // Point() {} // 1
    // Point(int a, int b) {} // 2
};

int main()
{
    Point p1; // ok
    Point p2 { 1, 2 }; // error
}
```

``` cpp
struct Point
{
    int x, y;
    
    void foo() {}
    // Point() = default;
    // Point() {} // 1
    // Point(int a, int b) {} // 2
};

int main()
{
    Point p1; // ok
    Point p2 { 1, 2 }; // ok
}
```

#### aggregate type: {}로 초기화 가능한것

- 구조체
- 배열
- 가상함수를 넣으면 aggregate아님

aggreaget type 조건은 cppreference 에 참고

---

> 초기화 문제점 해결

#### C++ 98 / 03 초기화 구문의 문제점

1. 객체의 종류에 따라 초기화 방법이 다르다
2. 클래스의 멤버로 있는 배열을 초기화 할 수 없다.
3. 동적 메모리 할당으로 만들어진 배열을 초기화 할 수 없다.
4. STL 컨테이너를 초기화하는 편리한 방법이 없다.

``` cpp
int main()
{
    // 1.
    int n1 {0};
    int n2 {0};

    int ar[3] {1,2,3};

    // 2.
    class Test
    {
        int x[10]{1,2,3,4,5,6,7,8,9,10};
    };

    // 3.
    int* p = new int[3]{1,2,3};

    // 4.
    vector<int> v{1,2,3};
}

```

모든 초기화 구문이 동일해 문제점 극복됨
