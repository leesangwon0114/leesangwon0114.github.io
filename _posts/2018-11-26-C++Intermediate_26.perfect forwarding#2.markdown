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

template<typename T> T&& xforward(T& arg)
{
    return static_cast<T&&>(arg);
}

template<typename F, typename T>
void chronometry(F f, T&& arg)
{
    f(std::xforward<T>(arg));
}

int main()
{
    int n = 0;
    chronometry(&goo, n);
    chronometry(&hoo, 1);
    cout << n << endl;
}
```

---

#### move 하고 forward 의 차이점

``` cpp

// static_cast<T&&>(arg); T의 타입에 따라 rvalue 또는 lvalue 캐스팅.

// 함수 인자 : lvalue 와 rvalue를 모두 받아서
// 리턴 타입 : rvalue 로 캐스팅.
template<typename T>
typename remove_reference<T>::type&&
xmove(T&& obj)
{
    return static_cast<typename remove_reference<T>::type &&>(obj);
}

// 함수 인자 : lvalue를 받아서
// 리턴 타입 : T에 따라서 lvalue or rvalue 로 캐스팅
template<typename T> T&& xforward(T& arg)
{
    return static_cast<T&&>(arg);
}
```

---

> std::forward #2

``` cpp
void foo(int& a) { cout << "int&" << endl; }
void foo(int&& a) { cout << "int&&" << endl; }

class Test
{
    int data;
public:
    int& get() & { return data; }
    int get() && { return data; }
};

//template<typename T> T&& xforward(T& arg) // -> xforward(arg); 에러 나오게 remove_reference 붙임
template<typename T> T&& xforward(typename remove_reference<T>::type& arg)
{
    return static_cast<T&&>(arg);
}

// rvalue 를 인자로 가지는 forward
// rvalue 를 인자로 받아서 rvalue 로 캐스팅하는 xforward
template<typename T> T&& xforward(typename remove_reference<T>::type&& arg)
{
    return static_cast<T&&>(arg);
}

template<typename T> void wrapper(T&& obj)
{
    // foo(std::forward<T>(obj).get()); //ok.
    using Type = decltype(xforward<T>(obj).get()); 
    foo(xforward<Type>(xforward<T>(obj).get())); // error
}

int main()
{
    Test t;

    wrapper(t); // lvalue => foo(int&)
    wrapper(Test()); // rvalue => foo(int&&)

    foo(t.get());   // foo(int&) => foo(int&)
    foo(Test().get()); // foo(int) => foo(int&&)
}
```

---

> setter using move & copy

``` cpp
#include <iostream>
using namespace std;

class Data
{
public:
    Data() {}
    ~Data() {}

    Data(const Data& t) { cout << "Copy" << endl;}
    Data(Data&& t) noexcept { cout << "Move" << endl;}
    Data& operator=(const Data& t) { cout << "Copy=" << endl; return *this; }
    Data& operator(Data&& t) noexcpet { cout << "Move=" << endl; return *this; }
};

class Test
{
    Data data;
public:
    //void setData(Data d) { data = d; }

    // 아래 코드는 무조건 copy= 사용
    void setData(const Data& d) { data = d; }
};

int main()
{
    Test test;
    Data d;
    test.setData(d);    // 실행후에도 d를 사용가능
    test.setData(move(d)); // 실행후에는 d를 사용불가
}
```

Copy= Copy= 출력됨


``` cpp
#include <iostream>
using namespace std;

class Data
{
public:
    Data() {}
    ~Data() {}

    Data(const Data& t) { cout << "Copy" << endl;}
    Data(Data&& t) noexcept { cout << "Move" << endl;}
    Data& operator=(const Data& t) { cout << "Copy=" << endl; return *this; }
    Data& operator(Data&& t) noexcpet { cout << "Move=" << endl; return *this; }
};

class Test
{
    Data data;
public:
    // 아래 코드는 무조건 move=이 아니고 copy= 임(상수 객체는 move 될 수 없음)
    void setData(const Data& d) { data = move(d); }
};

int main()
{
    Test test;
    Data d;
    test.setData(d);    // 실행후에도 d를 사용가능
    test.setData(move(d)); // 실행후에는 d를 사용불가
}
```

Copy= Copy= 출력됨

---

#### 해결책 #1 move setter 와 copy setter 를 별도의 함수로 제공

장점 : 오버헤드가 없다.

단점 : setter 함수를 2개를 제공해야 한다.

``` cpp
#include <iostream>
using namespace std;

class Data
{
public:
    Data() {}
    ~Data() {}

    Data(const Data& t) { cout << "Copy" << endl;}
    Data(Data&& t) noexcept { cout << "Move" << endl;}
    Data& operator=(const Data& t) { cout << "Copy=" << endl; return *this; }
    Data& operator(Data&& t) noexcpet { cout << "Move=" << endl; return *this; }
};

class Test
{
    Data data;
public:
    void setData(const Data& d) { data = d; }
    void setData(Data&& d) { data = move(d); }
};

int main()
{
    Test test;
    Data d;
    test.setData(d);    // copy=
    test.setData(move(d)); // move=
}
```

---

#### 해결책 #2 call by value를 사용하는 방법

장점 : setter 함수는 한 개만 제공하면 된다.

단점 : 약간의 오버헤드가 있다.(move 1회)

``` cpp
#include <iostream>
using namespace std;

class Data
{
public:
    Data() {}
    ~Data() {}

    Data(const Data& t) { cout << "Copy" << endl;}
    Data(Data&& t) noexcept { cout << "Move" << endl;}
    Data& operator=(const Data& t) { cout << "Copy=" << endl; return *this; }
    Data& operator(Data&& t) noexcpet { cout << "Move=" << endl; return *this; }
};

class Test
{
    Data data;
public:
    // call by value
    void setData(Data d) { data = move(d); }
};

int main()
{
    Test test;
    Data d;
    test.setData(d);    // copy=
                        // copy 생성, move=
    test.setData(move(d)); // move=
                           // move 생성, move=
}
```

---

#### 해결책 #3 forwarding reference

장점 : 오버헤드가 없고, 하나의 함수 템플릿만 제공하면 된다.

단점 : side effect 를 고려해야한다.(템플릿이니까 꼭 데이터 타입이 아니여도 함수가 생성될 수 있음... 뒤에 막는 기법 설명)

``` cpp
#include <iostream>
using namespace std;

class Data
{
public:
    Data() {}
    ~Data() {}

    Data(const Data& t) { cout << "Copy" << endl;}
    Data(Data&& t) noexcept { cout << "Move" << endl;}
    Data& operator=(const Data& t) { cout << "Copy=" << endl; return *this; }
    Data& operator(Data&& t) noexcpet { cout << "Move=" << endl; return *this; }
};

class Test
{
    Data data;
public:
    template<typename T> void setData(T&& a)
    {
        data = std::forward<T>(a);
    }
};

int main()
{
    Test test;
    Data d;
    test.setData(d);    // copy=
    test.setData(move(d)); // move=
}
```
