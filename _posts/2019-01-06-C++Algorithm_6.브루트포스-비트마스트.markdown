---
layout: post
title:  "C++Algorithm 6.&nbsp;브루트포스 - 비트마스크"
date:   2019-01-06 02:18:15 +0700
categories: [Algorithm, c++]
---

codeplus 백준 강사 강의 내용기반으로 정리한 내용입니다.(비공개 포스트)

---

> 비트마스크 사용하기

비트마스크를 이용해 모든 경우의 수를 만드는 것

집합에는 같은 수가 없으므로 비트마스크 사용해도됨

0부터 N-1까지 이루어진 집합에서 사용

값 확인은 &, 값을 추가할 때는 |, 값을 지우려고할 때는 지우고 싶은 수 만 0으로 바꾸고 나머지는 1로 채워 &, 토글(1->0, 0->1) XOR 연산 ^ 로 해결

#### 집합 문제

[집합 문제](https://www.acmicpc.net/problem/11723)

``` cpp
#include <iostream>
#include <cstdio>
#include <string>
using namespace std;

int main()
{
  cin.tie(NULL);
  ios::sync_with_stdio(false);
  int M = 0;
  cin >> M;
  int s = 0;
  for (int i = 0; i < M; ++i) {
    string operate;
    int n = 0;
    cin >> operate;
    if (operate == "add") {
      cin >> n;
      s |= (1 << n-1);
    }
    else if (operate == "remove") {
      cin >> n;
      s &= ~(1 << n-1);
    }
    else if (operate == "check") {
      cin >> n;
      int c = s & (1 << n - 1);
      if (c > 0)
        cout << "1\n";
      else
        cout << "0\n";
    }
    else if (operate == "toggle") {
      cin >> n;
      s ^= (1 << n-1);
    }
    else if (operate == "all") {
      for (int j = 0; j < 20; j++) {
        s |= (1 << j);
      }
    }
    else if (operate == "empty") {
      s = 0;
    }
  }
  return 0;
}
```

#### 부분집합의 합 문제

크기가 N인 모든 부분집합(i=1부터 시작되어 공집합 제외됨)

``` cpp
for (int i=1; i<(1<<n); i++) {
  int sum = 0;
  for (int k=0; k<n; k++) {
    if(i&(1<<k)) {
      sum += a[k];
    }
  }
}
```

n = 2 이면 1, 2, 3 출력됨(i 출력시)

-> 시간복잡도 O(NX2^N)
