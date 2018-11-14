---
layout: post
title:  "C++Intermediate&nbsp;18.&nbsp;temporary object#1"
date:   2018-11-14 04:18:15 +0700
categories: [c++]
---

codenuri 강석민 강사 강의 내용기반으로 정리한 내용입니다.

---

> 임시객체의 개념과 수명

```cpp
#include <iostream>
using namespace std;

int main()
{
    int a = 1, b = 2, c = 3;

    int sum = a + b + c; // int temp = a + b;
                         // int sum = temp + c;
}
```

컴파일러가 컴파일하다가 필요에 의해 만든 temp 같은 변수를 임시객체라함

전통적인 임시객체는 컴파일러에 의해 만들어지는 임시 메모리 공간

C++은 이 개념에 확장해 사용자가 임시객체를 만들 수 있는 등 다양한 기법 존재

---

Point.h

``` cpp
#include <iostream>
class Point
{
public:
    int x, y;

    Point (int a = 0, int b = 0)
    {
        std::cout << "Point()" << std::endl;
    }
    Point (const Point&)
    {
        std::cout << "Point(constPoint&)" << std::endl;
    }
    ~Point()
    {
        std::cout << "~Point()" << std::endl;
    }
};

```

``` cpp
#include <iostream>
#include "Point.h"
using namespace std;;

int main()
{
    Point p1(1,1); // 이름 : p1, 파괴 : 함수{}을 벗어날 때.
                   // named object
    Point(1,1);    // 이름 : 없다, 파괴 : 문장의 끝(;)
                   // 임시객체 : 클래스이름()
                   // unnamed object
    cout << "end" << endl;
}
```

객체는 Point() end ~Point() 순으로 출력

임시객체는 Point() ~Point() end 순으로 출력

``` cpp
#include <iostream>
#include "Point.h"
using namespace std;;

int main()
{
    Point(1, 1), cout << "X" << endl;
    cout << "end" << endl;
}
```

Point() X ~Point() end 출력

---

> 임시 객체의 특징

``` cpp
#include <iostream>
#include "Point.h"
using namespace std;

int main()
{
    Point pt(1,1); // pt: 이름있는 객체
    pt.x = 10; // ok.

    Point(1,1).x = 10; // error. 특징 1. 임시객체는 lvalue 가 될 수 없다.

    Point* p1 = &pt; // ok.

    Point* p2 = &Point(1,1); // error. 특징 2. 임시객체는 주소를 구할 수 없다.

    Point& r1 = pt; // ok.
    Point& r2 = Point(1, 1); // error. 특징 3. 임시객체는 non const 참조할 수 없다.(cl컴파일러만 예외로 확장문법을 사용해 허용, Za옵션으로 확장 문법 끄면됨)

    const Point& r3 = pt; // ok.
    const Point& r4 = Point(1,1); // ok. 특징 4. 임시객체는 const 참조로 참조할 수 있다. -> 임시객체의 수명이 r4의 수명과 동일해 진다.

    // C++11 rvalue reference
    Point&& r5 = Point(1, 1); // ok. 특징 5. 임시객체는 rvalue 참조로 참조할 수 있다.
    r5.x = 10; // ok.
}
```

---

> 임시 객체와 함수

임시객체를 어떻게 활용하고 장점이 있는지 살펴보자

``` cpp
#include <iostream>
#include "Point.h"
using namespace std;

// 임시객체와 함수 인자
void foo(const Point& p)
{

}

int main()
{
    // foo의 인자로 주는데 문장의 끝에 도달해서 파괴됨
    Point pt(1, 1);
    foo(pt);

    // 임시객체를 사용한 인자전달
    foo(Point(1,1));

    cout << "end" << endl;
}
```

단지 함수인자를 전달하기 위한 객체를 만들면 임시객체를 이용한 인자전달이 메모리에 효과적

sort(x, x+10, greater<int>()); 와 같이 stl도 임시객체 이용(greater)

따라서 받을 때는 무조건 const 참조로 받아야함(일반 참조로 받으면 에러 발생)

-> ms 확장문법은 가능하지만 안쓰는 것이 좋음

---

``` cpp
#include <iostream>
#include "Point.h"
using namespace std;

Point foo()
{
    Point pt(1, 1); // 2. 생성자
    return pt; // return Point(pt) 임시객체 생성 -> pt와 같게 만들려고 3. 복사생성자
}

int main()
{
    Point ret(0,0); // 1. 생성자

    ret = foo(); // 4. 임시객체가 대입연산자를 사용해 ret에 대입됨

    cout << "end" << endl; // 5. end 출력

    // ret 소멸자가 불림
}
```

Point() Point() Point(const Point&) ~Point() ~Point() end ~Point() 순으로 출력됨

임시객체는 성능저하의 원인으로 return을 만들 때 아래와 같이 만듬


``` cpp
#include <iostream>
#include "Point.h"
using namespace std;

Point foo()
{
    return Point(1,1);
}

int main()
{
    Point ret(0,0);

    ret = foo();

    cout << "end" << endl;
}
```

Point() Point() ~Point() end ~Point() 순으로 출력

-> RVO(Return Value Optimization)

요즘 컴파일러는 첫번째 처럼 만들어도 컴파일러가 바꾸어 버림

-> NRVO(Named RVO) g++은 이런식으로 코드를 바꾸어서 복사생성자가 불리지 않았던 것임

-> MS 도 O2라는 최적화 옵션을 주면 NRVO 적용해 복사생성자가 불리지 않도록 할 수 있음

