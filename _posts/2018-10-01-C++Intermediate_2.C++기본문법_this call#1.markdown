---
layout: post
title:  "C++Intermediate&nbsp;1.&nbsp;C++기본문법_this call#1"
date:   2018-10-01 04:18:15 +0700
categories: [c++]
---

codenuri 강석민 강사 강의 내용기반으로 정리한 내용입니다.

---

> 멤버 함수의 호출 원리


```cpp
class Point
{
    int x = 0, y = 0;
public:
    void set(int a, int b)
    {
        x = a;
        y = b;
    }

    static void foo(int a)  // void foo (int a)
    {
        x = a;  // this-> x = a; 번경해야하는데... this가 없어서 error
        // 따라서 static에서는 멤버변수를 사용할 수 없는 문법이 나오는 것임
    }
}

int main()
{
    Point::foo(10); // push 10
                    // call Point::foo
    Point p1;
    Point p2;

    p1.set(10, 20); // 원리를 생각해 봅시다.
}
```

멤버함수는 객체마다(p1, p2) 만들어지는 것이 아니라 하나밖에 없음

**p1.set일 때 어떤 객체의 set 인지 어떻게 알 수 있을까?**

p1.set(10, 20) 을 보면 인자가 2개 밖에 없어보이지만 컴파일 시

- 컴파일러가 set(&p1, 10, 20) 으로 변경해줌
- 따라서 set(Point* const this, int a, int b)
- x = a; -> this->x = a;
- y = b; -> this->y = b;

set(&p1, 10, 20) 을 어셈블리로 나타내면

push 20

push 10

lea ecx, &p1    rcx, &p1 // 객체 주소는 레지스터로

call Point::set

위와 같이 호출되는 과정을 this call이라함

cl this1.cpp /FAs => this1.asm 어셈블리어 만들어짐

![Alt text](/static/img/C++Intermediate/2.1.PNG)

1.멤버함수는 1번째 인자로 객체의 주소(this)가 추가된다.

2.static 멤버함수는 객체의 주소가 추가되지 않는다.
