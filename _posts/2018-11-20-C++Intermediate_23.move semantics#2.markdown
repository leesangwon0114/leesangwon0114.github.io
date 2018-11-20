---
layout: post
title:  "C++Intermediate&nbsp;23.&nbsp;move semantics#2"
date:   2018-11-20 04:18:15 +0700
categories: [c++]
---

codenuri 강석민 강사 강의 내용기반으로 정리한 내용입니다.

---

> using move

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
    } // 복사 대입연산자
    Test& operator=(Test&& t) { 
        cout << "Move=" << endl;
        return *this; 
    } // move 대입연산자
};

template<typename T> void Swap(T& x, T& y)
{
    Test temp = x; // 복사 생성자
    x = y; // 복사 대입
    y = temp;
}

int main()
{
    Test t1, t2;
    Swap(t1, t2);
}
```

Copy Copy= Copy= 순으로 출력됨

Swap을 복사 없이 이동만으로 만들기

#### move 와 swap

``` cpp
#include <iostream>
#include <algorithm>

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
    } // 복사 대입연산자
    Test& operator=(Test&& t) { 
        cout << "Move=" << endl;
        return *this; 
    } // move 대입연산자
};

template<typename T> void Swap(T& x, T& y)
{
    Test temp = move(x);
    x = move(y);
    y = move(temp);
}

int main()
{
    Test t1, t2;
    // Swap(t1, t2);
    swap(t1, t2);
}
```

Move Move= Move= 순으로 출력됨

이미 STL의 Swap은 move 로 구현되어 있음

만일 move 를 안만들었다면 복사생성자가 불림

---

#### move 와 버퍼 복사

``` cpp
#include <iostream>
#include <vector>
#include <algorithm>

class Test
{
public:
    Test() {}
    ~Test() {}
    Test(const Test& t) { cout << "Copy" << endl; }
    // Test(Test&& t) { cout << "Move" << endl; }
    Test(Test&& t) noexcept { cout << "Move" << endl; }

    Test& operator=(const Test& t) { 
        cout << "Copy=" << endl;
        return *this; 
    } // 복사 대입연산자
    // Test& operator=(Test&& t) { 
    Test& operator=(Test&& t) noexcept { 
        cout << "Move=" << endl;
        return *this; 
    } // move 대입연산자
};

template<typename T> void Swap(T& x, T& y)
{
    Test temp = move(x);
    x = move(y);
    y = move(temp);
}

int main()
{
    Test* p1 = new Test[2];
    Test* p2 = new Test[4];

    for(int i = 0; i < 2; i++)
        // p2[i] = p1[i]; // copy 대입
        p2[i] = move(p1[i]); // move 대입

    vector<Test> v(2);
    v.resize(4);
}
```

작은 버퍼에서 큰 버퍼로 옮길 때 move 생성자를 쓰는 것이 성능 효과적

STL의 vector 가 move 로 되어 있음

noexcept 를 붙여야 Move 이며 Move 대입이 아니라 Move 생성을 사용함

---

> move & noexcept

STL이 어떻게 복사를 하고 있는지


``` cpp
#include <iostream>
#include <vector>
#include <algorithm>
#include <type_traits>

class Test
{
public:
    Test() { cout << "Test()" << endl; }
    ~Test() { cout << "~Test()" << endl; }
    Test(const Test& t) { cout << "Copy" << endl; }
    Test(Test&& t) noexcept { cout << "Move" << endl; }

    Test& operator=(const Test& t) { 
        cout << "Copy=" << endl;
        return *this; 
    }
    Test& operator=(Test&& t) noexcept { 
        cout << "Move=" << endl;
        return *this; 
    }
};

template<typename T> void Swap(T& x, T& y)
{
    Test temp = move(x);
    x = move(y);
    y = move(temp);
}

int main()
{
    /*
    Test* p1 = new Test[2];
    Test* p2 = new Test[4];

    for(int i = 0; i < 2; i++)
        p2[i] = move(p1[i]); // move 대입
    */

    Test t1;
    Test t2 = t1; // copy
    Test t3 = move(t2); // move

    bool b = is_nothrow_move_constructible<Test>::value; // move 실패 여부를 조사할 수 있는 traits(예외 있으면 true)

    cout << b << endl;

    Test t4 = move_if_noexcept(t1); // t1의 move 생성자의 예외가 없으면 move로 있으면 copy로 옮김
}
```

move 생성자의 예외가 없을 때만 move를 하는 것이 안전한 코드이고 이를 위해 noexcept 키워드를 적어주어야함

---

> move를 활용한 버퍼 복사

``` cpp
#include <iostream>
#include <vector>
#include <algorithm>
#include <type_traits>
using namespace std;

class Test
{
public:
    Test() { cout << "Test()" << endl; }
    ~Test() { cout << "~Test()" << endl; }
    Test(const Test& t) { cout << "Copy" << endl; }
    Test(Test&& t) noexcept { cout << "Move" << endl; }

    Test& operator=(const Test& t) { 
        cout << "Copy=" << endl;
        return *this; 
    }
    Test& operator=(Test&& t) noexcept { 
        cout << "Move=" << endl;
        return *this; 
    }
};

int main()
{
    Test* p1 = new Test[2];
    // Test* p2 = new Test[4];
    // 1. 신규 버퍼는 메모리만 할당 한다.
    Test* p2 = static_cast<Test*>(operator new(sizeof(Test)*4));

    for(int i = 0; i < 2; i++)
    {
        // p2[i] = move(p1[i]);
        // new(&p2[i]) Test; // 디폴트 생성자 호출
        // new(&p2[i]) Test(p1[i]); // 복사 생성자
        // new(&p2[i]) Test(move(p1[i])); // move 생성자

        new(&p2[i]) Test(move_if_noexcept(p1[i]));
    }

    // 2. 새로운 객체는 디폴트 생성자 호출
    for(int i = 2; i < 4; i++)
    {
        new(&p2[i]) Test; // 디폴트 생성자 호출.
    }
}
```

---

``` cpp
#include <iostream>
#include <vector>
#include <algorithm>
#include <type_traits>
using namespace std;

class Test
{
public:
    Test() { cout << "Test()" << endl; }
    ~Test() { cout << "~Test()" << endl; }
    Test(const Test& t) { cout << "Copy" << endl; }
    Test(Test&& t) noexcept { cout << "Move" << endl; }

    Test& operator=(const Test& t) { 
        cout << "Copy=" << endl;
        return *this; 
    }
    Test& operator=(Test&& t) noexcept { 
        cout << "Move=" << endl;
        return *this; 
    }
};

int main()
{
    Test* p1 = static_cast<Test*>(operator new(sizeof(Test)*2));
    for(int i = 0; i < 2; i++)
    {
        new(&p1[i]) Test;
    }

    Test* p2 = static_cast<Test*>(operator new(sizeof(Test)*4));

    for(int i = 0; i < 2; i++)
    {
        new(&p2[i]) Test(move_if_noexcept(p1[i]));
    }
    for(int i = 2; i < 4; i++)
    {
        new(&p2[i]) Test;
    }

    // 최초 버퍼 파괴
    for(int i = 1; i >= 0; i--)
    {
        p1[i].~Test();
    }

    operator delete(p1);

    cout << "--" << endl;

    // 신규 버퍼 파괴
    for(int i = 3; i >= 0; i--)
    {
        p2[i].~Test();
    }

    operator delete(p2);
}
```

``` cpp
#include <iostream>
#include <vector>
using namespace std;

class Test
{
public:
    Test() { cout << "Test()" << endl; }
    ~Test() { cout << "~Test()" << endl; }
    Test(const Test& t) { cout << "Copy" << endl; }
    Test(Test&& t) noexcept { cout << "Move" << endl; }

    Test& operator=(const Test& t) { 
        cout << "Copy=" << endl;
        return *this; 
    }
    Test& operator=(Test&& t) noexcept { 
        cout << "Move=" << endl;
        return *this; 
    }
};

int main()
{
    vector<Test> v(2);
    v.resize(4);
    cout << "--" << endl;
}
```