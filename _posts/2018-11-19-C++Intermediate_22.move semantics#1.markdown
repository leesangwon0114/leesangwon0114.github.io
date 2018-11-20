---
layout: post
title:  "C++Intermediate&nbsp;22.&nbsp;move semantics#1"
date:   2018-11-19 04:18:15 +0700
categories: [c++]
---

codenuri 강석민 강사 강의 내용기반으로 정리한 내용입니다.

---

> copy constructor

``` cpp
class Point
{
    int x, y;
public:
    Point(int a = 0, int b = 0) : x(a), y(b) {}

    // Point(Point p) // Point p = p1. 결국 복사 생성자가 무한히 호출
    // Point(Point& p) // 빌드하는데의 문제는 없음. p3를 만들 때 임시객체이므로 lvalue 로 참조 불가능함(lvalue 객체만 인자로 받을 수 있다. 함수 리턴 값으로 반환되는 임시객체를 받을 수 없다.)
    Point(const Point& p) // lvalue 객체와 rvalue 객체를 모두 받을 수 있음
    {
        // 모든 멤버 복사
    }
};

Point foo()
{
    Point ret(0, 0);
    return ret;
}

int main()
{
    Point p1(1,1); // 생성자 호출
    Point p2(p1); // Point(Point) : 복사 생성자

    Point p3(foo());
}
```

---

> move semantics

``` cpp
class Cat
{
    char& name;
    int age;
public:
    Cat(const char* n, int a) : age(a)
    {
        name = new char[strlen(n) + 1];
        strcpy(name, n);
    }
    ~Cat() { delete[] name; }
    // 깊은 복사로 구현한 복사 생성자
    Cat(const Cat& c) : age(c.age)
    {
        name new char[strlen(c.name) + 1];
        strcpy(name, c.name);
    }

};

int main()
{
    Cat c1("NABI", 2);
    Cat c2 = c1; // runtime error (메모리 두번 참조로 하나 파괴시 다음번 또 파괴하려함 -> 복사생성자 깊은 복사 이용)
}
```

---

``` cpp
class Cat
{
    char& name;
    int age;
public:
    Cat(const char* n, int a) : age(a)
    {
        name = new char[strlen(n) + 1];
        strcpy(name, n);
    }
    ~Cat() { delete[] name; }
    // 깊은 복사로 구현한 복사 생성자
    Cat(const Cat& c) : age(c.age)
    {
        name new char[strlen(c.name) + 1];
        strcpy(name, c.name);
    }
    // 소유권 이전(자원전달)의 복사 생성자-> rvalue reference 로 임시객체만 올 수 있도록함
    Cat(Cat&& c) : age(c.age), name(c.name) // 이동생성자(move)라 불림(C++11 에서는 두 개 만듬)
    {
        c.name = 0; // 자원 포기
    }
};

Cat foo() // 값 리턴 : 임시객체(rvalue)
{
    Cat cat("NABI", 2);
    return cat;
}

int main()
{
    Cat c = foo(); // 임시객체
}
```

임시객체로 만들고 복사 후 파괴하면... 자원이 비효율적임

name 을 깊은 복사하지말고 주소만 가르키게 함

임시객체가 파괴될 때 주소 복사 후 임시객체의 name 주소를 0으로 바꾸어 임시객체 파괴 시 0은 delete 되는 데 문제 없음

따라서 성능 향상됨

---

#### move 정리

``` cpp
class Test
{
    int* resource;
public:
    Test() {} // 자원 할당
    ~Test() {} // 자원 해지

    // 복사생성자 : 깊은 복사 또는 참조계수
    // 인자로 lvalue 와 rvalue를 모두 받을 수 있다
    Test(const Test& t) { cout << "Copy" << endl; }

    // Move 생성자 : 소유권 이전(자원 전달)
    // rvalue 만 전달 받을 수 있다.
    Test(Test&& t) { cout << "Move" << endl; }
};

int main()
{
    Test t1;
    Test t2 = t1; // 복사생성자
    Test t3 = Test(); // move 생성자(단, move 를 안만들면 복사생성자)
}
```

move 생성자를 안만들어도 에러가 없지만 만들면 성능향상을 볼 수 있음

Test t3 = Test(); 일 경우 컴파일러 최적화로 컴파일 시 바로 생성자 불러서 만들어 버려 Copy 한번 밖에 안불리는 것 볼 수 있음(g++ 에서 -fno-elide-constructors 옵션을 주면됨)

---

> std::move

``` cpp
class Test
{
public:
    Test() {}
    ~Test() {}
    Test(const Test& t) { cout << "Copy" << endl; }
    Test(Test&& t) { cout << "Move" << endl; }
};

int main()
{
    Test t1;
    Test t2 = t1;     // Copy
    Test t3 = Test(); // Move -> g++ -fno-elide-constructors 옵션 붙여야함
    Test t4 = static_cast<Test&&>(t1); // Move - 복사가 아닌 move 생성자를 호출해 달라.

    Test t5 = move(t2); // move 가 내부적으로 위처럼 캐스팅한다.
}
```

Copy Move Move 순으로 출력됨

---

``` cpp
class Test
{
public:
    Test() {}
    ~Test() {}
    Test(const Test& t) { cout << "Copy" << endl; }
    Test(Test&& t) { cout << "Move" << endl; }

    Test& operator=(const Test& t) { return *this; } // 복사 대입연산자
    Test& operator=(Test&& t) { return *this; } // move 대입연산자
};

int main()
{
    Test t1;
    Test t2 = t1;     // 초기화. 복사 생성자
    t2 = t1;          // 대입. 대입 연산자

    t2 = move(t1);
}
```

복사 대입과 move 대입 연산자도 만들어야한다.
