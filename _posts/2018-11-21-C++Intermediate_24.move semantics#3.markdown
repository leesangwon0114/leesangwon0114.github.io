---
layout: post
title:  "C++Intermediate&nbsp;24.&nbsp;move semantics#3"
date:   2018-11-21 04:18:15 +0700
categories: [c++]
---

codenuri 강석민 강사 강의 내용기반으로 정리한 내용입니다.

---

> std::move implementation

``` cpp
#include <iostream>

class Test
{
public:
    Test() {}
    ~Test() {}
    Test(const Test& t) { cout << "Copy" << endl; }
    Test(Test&& t) { cout << "Move" << endl; }

    Test& operator=(const Test& t) { 
        cout << "Copy=" << endl;
        return *this; 
    }
    Test& operator=(Test&& t) { 
        cout << "Move=" << endl;
        return *this; 
    }
};

// T& : lvalue 만 받을 수 있다.
// T&& : lvalue와 rvalue를 모두 받을 수 있다.
template<typename T> T&& xmove(T&& obj)
{
    return static_cast<T&&>(obj);
}

int main()
{
    Test t1;
    Test t2 = t1;       // copy
    Test t3 = xmove(t1); // move -> static_cast<Test&&>(t1); 

    Test t4 = xmove(Test()); // move
}
```

Test t3 = xmove(t1); 이 copy로 출력되 잘못됨(문제!)

인자로 lvalue가 오면 lvalue로 rvalue 가 오면 rvalue로 되어 목표인 rvalue로 캐스팅하는 것과 다르게 동작함

typename remove_reference<T>::type 도입

``` cpp
#include <iostream>

class Test
{
public:
    Test() {}
    ~Test() {}
    Test(const Test& t) { cout << "Copy" << endl; }
    Test(Test&& t) { cout << "Move" << endl; }

    Test& operator=(const Test& t) { 
        cout << "Copy=" << endl;
        return *this; 
    }
    Test& operator=(Test&& t) { 
        cout << "Move=" << endl;
        return *this; 
    }
};

// T& : lvalue 만 받을 수 있다.
// T&& : lvalue와 rvalue를 모두 받을 수 있다.
// 인자로 lvalue 전달 : T => Test&  T&& : Test& && => Test&
//        rvalue 전달 : T => Test   T&& : Test && => Test&&
template<typename T> typename remove_reference<T>::type xmove(T&& obj)
{
    // 목표 : rvalue 로 캐스팅
    // return static_cast<T&&>(obj);
    return static_cast<typename remove_reference<T>::type &&>(obj);
}

int main()
{
    Test t1;
    Test t2 = t1;       // copy
    Test t3 = xmove(t1); // move -> static_cast<Test&&>(t1);

    Test t4 = xmove(Test()); // move
}
```

---

> move & const object

``` cpp
#include <iostream>

class Test
{
public:
    Test() {}
    ~Test() {}
    Test(const Test& t) { cout << "Copy" << endl; }
    Test(Test&& t) { cout << "Move" << endl; }

    Test& operator=(const Test& t) { 
        cout << "Copy=" << endl;
        return *this; 
    }
    Test& operator=(Test&& t) { 
        cout << "Move=" << endl;
        return *this; 
    }
};

int main()
{
    const Test t1;
    Test t2 = move(t1); // move ? copy ? error?
}
```

출력 : Copy

t1이 상수라 모든 멤버를 바꿀 수 없는데 pointer를 리셋할 수 없으니까 move 될 수 없음

``` cpp
#include <iostream>

class Test
{
public:
    Test() {}
    ~Test() {}
    Test(const Test& t) { cout << "Copy" << endl; }
    Test(Test&& t) { cout << "Move" << endl; }

    Test& operator=(const Test& t) { 
        cout << "Copy=" << endl;
        return *this; 
    }
    Test& operator=(Test&& t) { 
        cout << "Move=" << endl;
        return *this; 
    }
};

// lvalue를 전달하면 T : lvalue 참조. => const Test&
template<typename T> typename remove_reference<T>::type xmove(T&& obj)
{
    // const Test&& => Test(Test&&) - move (move로 부를 수 있는 타입이 아님)
    //              => Test(const Test&) - copy (lvalue, rvalue, const lvalue, const rvalue 모두 받을 수 있음 복사생성자는)
    // return static_cast<const Test&&>(obj);
    return static_cast<typename remove_reference<T>::type &&>(obj);
}

int main()
{
    const Test t1;
    
    // Test t2 = static_cast<Test&&>(t1); // error 상수를 비상수로 캐스팅 불가

    Test t2 = xmove(t1);
}
```

상수객체는 move를 사용할 수 없지만 error는 안나고 복사로 수행됨

---

> implementation

``` cpp
#include <iostream>
#include <string>
class Test
{
public:
    Test() {}
    ~Test() {}
    Test(const Test& t) { cout << "Copy" << endl; }
    Test(Test&& t) { cout << "Move" << endl; }

    Test& operator=(const Test& t) { 
        cout << "Copy=" << endl;
        return *this; 
    }
    Test& operator=(Test&& t) { 
        cout << "Move=" << endl;
        return *this; 
    }
};

class Buffer
{
    size_t sz;
    int* buf;
    string tag;
    Test test;
public:
    Buffer(size_t s, string t) : sz(s), tag(t), buf(new int[s]) {}
    ~Buffer() { delete[] buf; }

    // 깊은 복사
    Buffer(const Buffer& b) : sz(b.sz), tag(b.tag), test(b.test)
    {
        buf = new int[sz];
        memcpy(buf, b.buf, sizeof(int)*sz);
    }

    // move 생성자
    Buffer(Buffer&& b) : sz(b.sz), tag(b.tag), test(b.test)
    {
        buf = b.buf;
        b.buf = 0; // 자원 포기
    }
};

int main()
{
    Buffer b1(1024, "SHARED");
    Buffer b2 = b1; // copy

    Buffer b2 = move(b1); // copy
}
```

move를 만들 때는 모든 멤버들도 move 로 옮겨야함

``` cpp
#include <iostream>
#include <string>
class Test
{
public:
    Test() {}
    ~Test() {}
    Test(const Test& t) { cout << "Copy" << endl; }
    Test(Test&& t) { cout << "Move" << endl; }

    Test& operator=(const Test& t) { 
        cout << "Copy=" << endl;
        return *this; 
    }
    Test& operator=(Test&& t) { 
        cout << "Move=" << endl;
        return *this; 
    }
};

class Buffer
{
    size_t sz;
    int* buf;
    string tag;
    Test test;
public:
    Buffer(size_t s, string t) : sz(s), tag(t), buf(new int[s]) {}
    ~Buffer() { delete[] buf; }

    // 깊은 복사
    Buffer(const Buffer& b) : sz(b.sz), tag(b.tag), test(b.test)
    {
        buf = new int[sz];
        memcpy(buf, b.buf, sizeof(int)*sz);
    }

    // move 생성자 : 모든 멤버를 move로 옮기도록 작성한다.
    Buffer(Buffer&& b) noexcept : sz(move(b.sz)), tag(move(b.tag)), test(move(b.test))
    {
        buf = move(b.buf);
        b.buf = 0; // 자원 포기
    }
};

int main()
{
    Buffer b1(1024, "SHARED");
    // Buffer b2 = b1; // copy

    Buffer b2 = move(b1); // move
}
```

기본형은 안써도 되지만 객체형은 무조건 move로 옮겨야 move 가 출력됨

move할 때 예외가 없어야하는데 각 객체에 대한 move 에 대해 예외가 없다고 해주어야함

-> 이때 noexcept 붙여줌

---

int* buf;  이런 포인터 없다고 가정해보자

복사생성자를 만들지 않으면 컴파일러가 복사 생성자를 만들어주는 것 처럼 사용자가 move 생성자를 만들지 않으면 컴파일러가 move 생성자를 만들어 주었을 것임

``` cpp
#include <iostream>
#include <string>
class Test
{
public:
    Test() {}
    ~Test() {}
    Test(const Test& t) { cout << "Copy" << endl; }
    Test(Test&& t) { cout << "Move" << endl; }

    Test& operator=(const Test& t) { 
        cout << "Copy=" << endl;
        return *this; 
    }
    Test& operator=(Test&& t) { 
        cout << "Move=" << endl;
        return *this; 
    }
};

class Buffer
{
    size_t sz;
    string tag;
    Test test;
public:
    Buffer(size_t s, string t) : sz(s), tag(t), buf(new int[s]) {}
    ~Buffer() { delete[] buf; }

    // 사용자가 만들지 않으면 컴파일러가 아래 모양 제공.
    // 얕은 복사
    /*
    Buffer(const Buffer& b) : sz(b.sz), tag(b.tag), test(b.test)
    {
    }
    */

    /*
    Buffer(Buffer&& b) noexcept : sz(move(b.sz)), tag(move(b.tag)), test(move(b.test))
    {
    }
    */
};

int main()
{
    Buffer b1(1024, "SHARED");
    Buffer b2 = b1; // copy
    Buffer b3 = move(b1); // move
}
```

move, copy 모두 안 만들어도 알아서 만들어줌

포인터가 있을 때 스마트 포인터를 써 자원관리도 신경안쓰고 move, copy도 컴파일러가 알아서 해줌

#### 만일 사용자가 복사생성자만 제공했다면?

move 대신에 copy가 불림

-> 그냥 복사가 사용됨

둘 다 안 만들 경우만 컴파일러가 만들어줌

---

> value categories

``` cpp
#include <iostream>
using namespace std;

void f1(int a) {}
void f2(int& a) {}
void f3(int&& a) {}

int main()
{
    int n = 10;

    f1(n);
    f2(n);
    f3(static_cast<int&&>(n)); // f3(move(n))
}

// 리턴 타입에 따른 리턴 방법.
int x = 0;
int f4() { return x; }
int& f5() { return x; }
int&& f6() { return move(x); }
```

1. 지역변수는 참조로 리턴 할 수 없다
2. 함수 리턴 타입의 종류에 따라서 리턴하는 방법(value type, lvalue reference, rvalue reference)

---

``` cpp
#include <iostream>
using namespace std;

int x = 0;
int f1() { return x; }
int& f2() { return x; }
int&& f3() { return move(x); }

int main()
{
    f1() = 10; // error
    f2() = 20; // ok
    f3() = 30; // error
}
``` 

``` cpp
struct Base
{
    virtual void foo() { cout << "B::foo" << endl; }
};
struct Derived : Base
{
    virtual void foo() { cout << "D::foo" << endl; }
};

Derived d;
Base f1() {return d;}
Base& f2() {return d;}
Base&& f3() {return move(d);}

int main()
{
    Base b1 = f1(); // 임시객체, move
    Base b2 = f2(); // copy
    Base b3 = f3(); // move

    f1().foo(); // B:foo (Base의 임시객체) non-다형적
    f2().foo(); // D::foo 다형적
    f3().foo(); // D::foo 다형적

    int n = 10;
    n = 20; // n : lvalue, 20 : prvalue
    int n3 = move(n); // xvalue (rvalue reference 로 리턴되는 모든 표현식은 xvalue로 불림)
}
```

rvalue reference 로 경계가 모호해짐 (prvalue, xvalue 등 새로운 용어 등장)

![Alt text](/static/img/C++Intermediate/24.1.PNG)

cppreference.com 사이트에 어떤 요소를 분리하는지 잘 정리해놓은 것 참조

