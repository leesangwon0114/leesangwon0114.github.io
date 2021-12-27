---
layout: post
title:  "C++Algorithm 2.&nbsp;브루트포스 - 브루트포스"
date:   2019-01-01 02:18:15 +0700
categories: [Algorithm, c++]
---

codeplus 백준 강사 강의 내용기반으로 정리한 내용입니다.(비공개 포스트)

---

> 브루트 포스

모든 경우의 수를 다 해보는 것

단, 경우의 수를 다 해보는데 걸리는 시간이 문제의 시간 제한을 넘지 않아야함

#### 3가지 단계 생각

- 문제의 가능한 경우의 수를 계산해본다.(직접 손으로 계산)
- 가능한 모든 방법을 다 만들어본다.(하나도 빠짐 없이 만들어야함 -> for문 사용, 순열사용, 재귀호출사용, 비트마스크 사용)
- 각각의 방법을 이용해 답을 구해본다.

---

> 그냥 다 해보기

#### 일곱 난쟁이 문제

[일곱 난쟁이 문제](https://www.acmicpc.net/problem/2309)

아홉명 중에 일곱 명을 고르는 것은 아홉명 중에 두명을 고르는 것과 같다.

9C2 = 36가지

두명을 고르는 경우의 수 (N^2 -> NC2)

합을 고르는 시간 복잡도 : O(N)

총 O(N^2) X O(N) = O(N^3) 이므로

N = 9 -> 729번(시간 내에 계산 가능)

``` cpp
#include <iostream>
#include <cstdio>
#include <vector>
#include <algorithm> 

using namespace std;

int main()
{
  vector<int> tall(9, 0);

  for (int i = 0; i < 9; ++i)
    cin >> tall[i];

  for (int i = 0; i < 8; ++i) {
    for (int j = i + 1; j < 9; ++j) {
      int sum = 0;
      for (int k = 0; k < 9; ++k) {
        if (k != i && k != j)
          sum += tall[k];
      }
      if (sum == 100) {
        vector<int> result;
        for (int p = 0; p < 9; ++p) {
          if (p != i && p != j)
            result.emplace_back(tall[p]);
        }
        sort(result.begin(), result.end());
        for (auto q = result.begin(); q != result.end(); ++q) {
          cout << *q << '\n';
        }
        return 0;
      }
    }
  }

  return 0;
}
```

-> 백준 강사는 미리 전체 sum을 더 해놓고, 두 변수를 빼주어 100이나오는지 확인하였음

안에 한번만 실행되는 for문은 시간복잡도에서 1이됨

---

#### 날짜 계산 문제

[날짜 계산 문제](https://www.acmicpc.net/problem/1476)

``` cpp
#include <iostream>

using namespace std;

int main()
{
  int E = 0;
  int S = 0;
  int M = 0;

  cin >> E >> S >> M;

  int e = 1;
  int s = 1;
  int m = 1;

  int result = 1;

  while (1) {
    if (e == E && s == S && m == M) {
      break;
    }
    e++;
    s++;
    m++;
    if (e > 15)
      e = 1;
    if (s > 28)
      s = 1;
    if (m > 19)
      m = 1;
    result++;
  }
  cout << result << endl;
  return 0;
}
```

-> mod를 이용해서 풀 수 도 있음

---

#### 테트로미노 문제

[테트로미노 문제](https://www.acmicpc.net/problem/14500)

``` cpp
#include <iostream>
#include <vector>
using namespace std;

int main()
{
  int N = 0;
  int M = 0;

  cin >> N >> M;

  vector < vector<int>> map(N, vector<int>(M, 0));

  int block[19][3][2] = {
    { { 0,1 },{ 0,2 },{ 0,3 } },
    { { 1,0 },{ 2,0 },{ 3,0 } },
    { { 1,0 },{ 1,1 },{ 1,2 } },
    { { 0,1 },{ 1,0 },{ 2,0 } },
    { { 0,1 },{ 0,2 },{ 1,2 } },
    { { 1,0 },{ 2,0 },{ 2,-1 } },
    { { 0,1 },{ 0,2 },{ -1,2 } },
    { { 1,0 },{ 2,0 },{ 2,1 } },
    { { 0,1 },{ 0,2 },{ 1,0 } },
    { { 0,1 },{ 1,1 },{ 2,1 } },
    { { 0,1 },{ 1,0 },{ 1,1 } },
    { { 0,1 },{ -1,1 },{ -1,2 } },
    { { 1,0 },{ 1,1 },{ 2,1 } },
    { { 0,1 },{ 1,1 },{ 1,2 } },
    { { 1,0 },{ 1,-1 },{ 2,-1 } },
    { { 0,1 },{ 0,2 },{ -1,1 } },
    { { 0,1 },{ 0,2 },{ 1,1 } },
    { { 1,0 },{ 2,0 },{ 1,1 } },
    { { 1,0 },{ 2,0 },{ 1,-1 } },
  };

  for (int i = 0; i < N; ++i) {
    for (int j = 0; j < M; ++j) {
      cin >> map[i][j];
    }
  }

  int result = 0;
  for (int i = 0; i < N; ++i) {
    for (int j = 0; j < M; ++j) {
      for (int k = 0; k < 19; ++k) {
        bool available = true;
        int sum = 0;
        sum += map[i][j];
        for (int p = 0; p < 3; ++p) {
          int x = i + block[k][p][0];
          int y = j + block[k][p][1];
          if (x >= 0 && x < N && y >= 0 && y < M) {
            sum += map[x][y];
          }
          else {
            available = false;
            break;
          }
        }
        if (available && sum > result)
          result = sum;
      }
    }
  }
  cout << result << endl;
  return 0;
}
```

-> block 변수를 통해 for 문 돌리기!
