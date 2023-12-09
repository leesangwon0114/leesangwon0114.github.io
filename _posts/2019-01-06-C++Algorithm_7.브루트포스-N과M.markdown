---
layout: post
title:  "C++Algorithm 7.&nbsp;브루트포스 - N과 M"
date:   2019-01-06 03:18:15 +0700
categories: [Algorithm, c++]
---

codeplus 백준 강사 강의 내용기반으로 정리한 내용입니다.(비공개 포스트)

---

> N과 M

재귀함수 연습해 볼 수 있는 문제들

#### N과 M(1) 문제

[N과 M(1) 문제](https://www.acmicpc.net/problem/15649)

``` cpp
#include <iostream>
#include <vector>
using namespace std;

class permutation {
  vector<bool> check;
  vector<int> result;
public:
  void solve(int n, int m, int index) {
    if (index == m) {
      for (int k = 0; k < m; ++k) {
        printf("%d ", result[k]);
      }
      printf("\n");
      return;
    }
    if (index > m) return;
    for (int i = 1; i <= n; ++i) {
      if (check[i]) continue;
      check[i] = true;
      result[index] = i;
      solve(n, m, index + 1);
      check[i] = false;
    }
  }
  void operator()(int n, int m) {
    check.assign(n + 1, 0);
    result.assign(n + 1, 0);
    solve(n, m, 0);
  }
};

int main() {
  int N = 0;
  int M = 0;
  scanf("%d %d", &N, &M);

  permutation p;
  p(N, M);
  return 0;
}
```

#### N과 M(2) 문제

[N과 M(2) 문제](https://www.acmicpc.net/problem/15650)

``` cpp
#include <iostream>
#include <vector>
using namespace std;

class permutation {
  vector<bool> check;
  vector<int> result;
public:
  void solve(int n, int m, int index) {
    if (index == m) {
      for (int k = 0; k < m; ++k) {
        printf("%d ", result[k]);
      }
      printf("\n");
      return;
    }
    if (index > m) return;
    for (int i = 1; i <= n; ++i) {
      if (check[i]) continue;
      check[i] = true;
      result[index] = i;
      solve(n, m, index + 1);
      check[i] = false;
    }
  }
  void operator()(int n, int m) {
    check.assign(n + 1, 0);
    result.assign(n + 1, 0);
    solve(n, m, 0);
  }
};

int main() {
  int N = 0;
  int M = 0;
  scanf("%d %d", &N, &M);

  permutation p;
  p(N, M);
  return 0;
}
```