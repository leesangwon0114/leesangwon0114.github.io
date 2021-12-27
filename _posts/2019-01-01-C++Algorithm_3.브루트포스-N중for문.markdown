---
layout: post
title:  "C++Algorithm;3.&nbsp;브루트포스 - N중 for문"
date:   2019-01-01 03:18:15 +0700
categories: [Algorithm, c++]
---

codeplus 백준 강사 강의 내용기반으로 정리한 내용입니다.(비공개 포스트)

---

> N중 for문

#### for

- N개 중에 일부를 선택해야 하는 경우에 많이 사용
- 재귀 호출이나 비트마스크를 사용하면 더 간결하고 보기 쉬운 코드를 작성할 확률이 높다.

#### 1,2,3 더하기 문제

[1,2,3 더하기 문제](https://www.acmicpc.net/problem/9095)

10중 for 문으로 해결해보기

``` cpp
// 1, 2, 3 더하기
#include <iostream>
using namespace std;

int main()
{
  int T = 0;
  cin >> T;
  while (T--) {
    int n = 0;
    cin >> n;

    int result = 0;
    for (int n0 = 1; n0 <= 3; ++n0) {
      if (n0 == n) result += 1;
      for (int n1 = 1; n1 <= 3; ++n1) {
        if (n0 + n1 == n) result += 1;
        for (int n2 = 1; n2 <= 3; ++n2) {
          if (n0 + n1 + n2 == n) result += 1;
          for (int n3 = 1; n3 <= 3; ++n3) {
            if (n0 + n1 + n2 + n3 == n) result += 1;
            for (int n4 = 1; n4 <= 3; ++n4) {
              if (n0 + n1 + n2 + n3 + n4 == n) result += 1;
              for (int n5 = 1; n5 <= 3; ++n5) {
                if (n0 + n1 + n2 + n3 + n4 + n5 == n) result += 1;
                for (int n6 = 1; n6 <= 3; ++n6) {
                  if (n0 + n1 + n2 + n3 + n4 + n5 + n6 == n) result += 1;
                  for (int n7 = 1; n7 <= 3; ++n7) {
                    if (n0 + n1 + n2 + n3 + n4 + n5 + n6 + n7 == n) result += 1;
                    for (int n8 = 1; n8 <= 3; ++n8) {
                      if (n0 + n1 + n2 + n3 + n4 + n5 + n6 + n7 +n8 == n) result += 1;
                      for (int n9 = 1; n9 <= 3; ++n9) {
                        if (n0 + n1 + n2 + n3 + n4 + n5 + n6 + n7 + n8 + n9 == n) result += 1;
                      }
                    }
                  }
                }
              }
            }
          }
        }
      }
    }
    cout << result << endl;
  }
  return 0;
}
```