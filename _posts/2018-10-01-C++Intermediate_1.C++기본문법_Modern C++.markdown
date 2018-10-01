---
layout: post
title:  "C++Intermediate&nbsp;1.&nbsp;C++기본문법_Modern C++"
date:   2018-10-01 03:18:15 +0700
categories: [c++]
---

codenuri 강석민 강사 강의 내용기반으로 정리한 내용입니다.

---

> About C++

www.isocpp.org

-> 현재 C++ 다양한 정보가 존재

Current ISO C++ Status

![Alt text](/static/img/C++Intermediate/1.1.PNG)

www.cppreference.com

-> C++ 표준 문서의 내용을 잘 설명함

> Hello, Modern C++

```cpp

// stop watch

template<typename F, typename ... A>
decltype(auto) chronometry(F&& f, A && ... args)
{
    stop_watch sw;
    return std::invoke(std::forward<F>(f), std::forward<A>(args)...);
}

class Hello
{
    std::string msg;
public:
    Hello(std::string s) : msg(s) {}

    void Say()
    {
        std::cout << msg << std::endl;
        std::this_thread::sleep_for(3s);
        std::cout << "bye..." << std::endl;
    }
}

int main()
{
    // 특정 함수의 실행시간을 측정하는 함수.
    chronometry(&Hello::Say, Hello("hello"s));
}


```

chronometry 를 만들기 위해 첫번째 맴버함수를 알아야하고 이를 위해 뒤에서 this를 설명

두번째 인자의 임시객체 특정과 뒤에 s를 붙이는 사용자정의 이터럴에 대해서도 배움

decltype을 위한 auto 설명

invoke 를 왜 사용하는지?

perfect forward가 무엇인지 사용하는 방법과 직접 만들어 봄

