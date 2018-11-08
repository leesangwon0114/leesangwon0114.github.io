---
layout: post
title:  "C++Intermediate&nbsp;12.&nbsp;C++11,14기본문법#2"
date:   2018-11-05 04:18:15 +0700
categories: [c++]
---

codenuri 강석민 강사 강의 내용기반으로 정리한 내용입니다.

---

> delete function

```cpp
#include <iostream>
using namespace std;

void foo(int n)
{
    cout << "foo(int)" << endl;
}

int main()
{
    foo(3.4); // foo(double) -> double이 int 로 암시적 변환(버그의 원인이 될 수 있음)
}
```

```cpp
#include <iostream>
using namespace std;

void foo(int n)
{
    cout << "foo(int)" << endl;
}

void foo(double); // 선언만

int main()
{
    foo(3.4);
}
```

위의 코드는 컴파일 시에는 error 안나 라이브러리 만들 때 error 발생하지 않음

```cpp
#include <iostream>
using namespace std;

void foo(int n)
{
    cout << "foo(int)" << endl;
}

void foo(double) = double

int main()
{
    foo(3.4);
}
```

삭제된 함수를 쓰려고 해서 error 발생함(컴파일 타임)

함수의 선언을 적고 delete 를 붙이는 것을 함수 삭제 문법이라함

#### 함수 삭제 문법(delete function)
- C++11 에서 추가된 문법, 함수를 삭제해 달라는 문법
- 함수를 제공하지 않을 때 함수 호출 시 인자가 변환 가능한 타입을 가지는 동일 이름의 다른 함수 호출, 선언만 제공할 때 링크 에러, 삭제할 때 함수 호출 시 컴파일 에러

---

안 만들면 되지 왜 삭제하는지?

``` cpp
template<typename T> void goo(T a)
{

}

void goo(double) = delete;

class Mutex
{
public:
    Mutex(const Mutex&) = delete;
    void operator=(const Mutex&) = delete;
private:
    // Mutex(const Mutex&);
};

int main()
{
    goo(3.4);

    Mutex m1;
    Mutex m2 = m1;
}
```

Mutex m2=m1에서 복사생성자 만들텐데 옛날에는 class에 private으로 복사 생성자를 만들어 주었음

private으로 복사생성자를 만드는 것은 문법이 아니라 테크닉으로 이제는 delete 를 이용해 복사생성자와 대입연산자를 삭제함

#### 함수 삭제 활용
- 일반 함수 삭제 가능
- 함수 템플릿을 만들 때 특정 타입에 대해서 삭제
- 멤버함수 삭제, 복사생성자와 대입연산자를 삭제하는 기법이 널리 사용됨
- 싱글톤, 복사 금지 스마트 포인터(unique_ptr), mutex가 함수 삭제 기법을 사용하고 있음

---

> default function

``` cpp
#include <iostream>
#include <type_traits>
using namespace std;

struct Point
{
    int x, y;

    // Point() {}
    Point() = default;
    
    Point(const Point&) = default;

    Point(int a, int b) : x(a), y(b) {}
};

int main()
{
    Point p1{};

    cout << p1.x << endl; // default 생성자 - 0
    // 사용자가 제공하면 쓰레기 값...

    cout << is_trivially_constructible<Point>::value << endl;
}
```

Point() {} 는 사용자가 생성자를 제공한 것

Point() = default 는 컴파일러가 제공

대표적인 차이점으로 Point() {} 는 trivial 하지 않음(구현을 외부 소스파일을 뽑을 수 있으므로 선언만 보고 조사 불가능)

아무일도 하지 않을 때 default 생성자를 쓰는 것이 좋음

#### default function
- 함수삭제와는 반대개념으로 함수의 디폴트 구현을 제공해 달라는 표현
- 사용자가 생성자의 구현을 제공할 때와 default 구현을 제공 받을 때의 차이점
  - Point() {} - Trivial 하지 않으며 멤버가 0으로 초기화 되지 않음
  - Point() = default; - Trivial 하고 멤버가 0으로 초기화됨

---

> noexcept

``` cpp
#include <iostream>
using namespace std;

// C++98
int foo() // 예외가 있을수도 있고, 없을 수도 있다.
int foo() throw(int) // 이 함수는 int 형태의 예외가 있다는 의미.
int foo() throw() // 예외가 없다는 의미.
{
    return 0;
}

int main()
{

}
```

많은 개발자들이 모르고 컴파일러가 위에 따라 지켜지는 규칙이 잘 지켜지지 않음

어떤 종류의 예외는 중요하지 않고 예외가 있다 없다가 중요하다고 깨달음

C++11 버전에서 noexcept 문법 생김


``` cpp
#include <iostream>
using namespace std;

// C++98
int foo() // 예외가 있을수도 있고, 없을 수도 있다.
int foo() throw(int) // 이 함수는 int 형태의 예외가 있다는 의미.
int foo() throw() // 예외가 없다는 의미.
{
    return 0;
}

// C++11
void goo() noexcept(true) // 예외가 없다
void goo() noexcept // 위와 동일.
{
    // throw 1;
}

int main()
{
    try
    {
        goo();
    }
    catch(...)
    {
        cout << "exception..." << endl;
    }
}
```

#### 예외가 없음을 알리는 문법
- void foo() throw() = C++98/03
- void foo() noexcept
- void foo() noexcept(true)

noexcept 인데 thorw를 던지면 프로그램이 죽음

#### noexcept
- 함수가 예외가 없음을 나타냄, C++11에서 도입
- noexcept로 선언된 함수에서 예외가 발생하면 std::terminate 가 호출됨
- 예외가 없음을 표기하면 보다 좋은 최적화 코드를 얻을 수 있다.
- 예외가 있는지 없는지 조사할 수 있게 된다.

---

어떤 함수가 예외가 있는지 없는지 조사해보기

``` cpp
#include <iostream>
using namespace std;

void algo1()
{
    // 빠르지만 예외 가능성이 있다.
}
void algo2() noexcept
{
    // 느리지만 예외가 나오지 않는다.
}

class Test {};

class Test2 {
    Test2() {}
};

class Test3 {
    Test3() noexcept {}
};

int main()
{
    bool b1 = noexcept(algo1()); // 함수를 부른 결과가 예외가 있는지 없는지 조사
    bool b2 = noexcept(algo2());

    cout << b1 << ", " << b2 << endl; // (0,1)

    bool b3 = is_nothrow_constructible<Test>::value;
    cout << b3 << endl; // 1

    bool b4 = is_nothrow_constructible<Test2>::value;
    cout << b4 << endl; // 0

    bool b5 = is_nothrow_constructible<Test3>::value;
    cout << b5 << endl; // 0
}
```

#### noexcept
- noexcept specifier : 지정자
- noexcept operator : 연산자

#### 함수가 예외가 없음을 조사하는 방법
- noexcept operator 사용
- type traits 사용: is_nothrow_xxx<T>::value
- move_if_noexcept
- 실행시간이 아닌 컴파일 시간에 조사

---

> scoped enum

enum 상수의 단점을 개선한 새로운 enum 상수 문법

``` cpp
#include <iostream>
#include <type_traits>
using namespace std;

enum Color { red = 1, green = 2 };
int main()
{
    int n1 = Color::red;
    int n2 = red;

    int red = 0;
    int n3 = red; // red 가 어느 것인지 혼란일으킬 수 있음

    cout << typeid(underlying_type_t<Color>).name() << endl;
}
```

#### 기존 enum 상수의 단점
- 타입의 이름 없이 사용 가능: Unscoped enum
- 요소의 타입을 지정할 수 없다.

#### 요소의 타입을 알아내는 방법 - C++11
- type_traits 헤더
- underlying_type<Color>::type
- underlying_type_t<Color>

---

#### C++11에서 도입된 새로운 enum 상수
- enum class Color:char {red = 1, green = 2};
- 요소의 타입을 지정할 수 있다.
- 다른 타입으로 암시적 형변환 되지 않는다. 명시적 변환 필요
- 반드시 타입의 이름과 함께 사용 해야 한다: Scoped enum

``` cpp
#include <iostream>
#include <type_traits>
using namespace std;

// C++98/03
// enum Color { red = 1, green = 2 };

// C++11
enum class Color : char { red = 1, green = 2 };

int main()
{
    int n1 = Color::red; // error
    Color n2 = Color::red; // ok
    int n3 = static_cast<int>(Color::red); // ok
    int n4 = red; // error

    cout << typeid(underlying_type_t<Color>).name() << endl; // char
}
```

---

> user define literal

#### literals 의 종류
- integer: 11, 013, oxa
- floating: 14.56
- character: 'A', 'a'
- string: "hello"

``` cpp
#include <iostream>
using namespace std;

// int operator""k(int v) // error
// _ : 사용자 사용가능.
// _ 로 시작하지 않은 것 : reserved 미래 C++에서 사용하기 위해 -> _ 없으려면 unsigned long long 사용
int operator""k(unsigned long long v)
{
    return v * 1000;
}

int main()
{
    int n1 = 10k; // operator""k(10)
    cout << n1 << endl; // 10000
}

```

#### user define literal
- literal 뒤에 사용자 정의 접미사를 붙이는 문법
- literal이 값 뿐 아니라 단위를 가질 수 있게 된다.
- operator""suffix
- 사용자가 제공하는 접미사는 반드시 "_"로 시작해야 한다.
- _가 붙지 않은 것은 C++ 표준 라이브러리를 위해서 예약 되어 있음

---

#### user define literals 과 함수인자
- integer: operator""suffix(unsigned long long), operator""suffix(const char*)
- floating: operator""suffix(long double), operator""suffix(const char*)
- character: operator""suffix(char)
- string: operator""suffix(const char*, size_t)

``` cpp
#include <iostream>
using namespace std;

class second
{
    int value;
public:
    second(long long s) : value(s) {}
};

class minute
{
    int value;
public:
    minute(long long s) : value(s) {}
};

second operator""_s(unsigned long long v)
{
    return v;
}

minute operator""_m(unsigned long long v)
{
    return v;
}

int operator""k(unsigned long long v)
{
    return v * 1000;
}

int main()
{
    int n1 = 10k; // operator""k(10)
    cout << n1 << endl; // 10000

    second n1 = 10_s;
    minute n2 = 10_m;
}
```

#### user define literal 과 user define type
- 사용자 정의 접미사를 사용해서 literal로 부터 객체를 생성할 수 있다.


위의 예제가 이미 C++ 표준에 있음

``` cpp
#include <iostream>
#include <string>
using namespace std;
using namespace std::literals::string_literals; // error 나면 이거 열면됨
using namespace std::chrono; // s, m을 나타내는 것 존재

using namespace std::literals; // 모든 literals 열기

void foo(string s) { cout << "string" << endl; }
void foo(const char* s) { cout << "char*" << endl; }

int main()
{
    foo("hello"); // char*
    foo("hello"s); // string operator""s("hello");

    seconds s1 = 10s;
    minutes m1 = 10min;

    seconds s2 = 10min;
    cout << s2.count() << endl; // 600
}
```
