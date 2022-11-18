---
layout: post
title:  "C++Algorithm 4.&nbsp;브루트포스 - 순열"
date:   2019-01-01 04:18:15 +0700
categories: [Algorithm, c++]
published : false
---

codeplus 백준 강사 강의 내용기반으로 정리한 내용입니다.(비공개 포스트)

---

> 순열

#### 순열(Permutation)

- 1~N까지로 이루어진 수열
- 크기는 N이고 겹치는 숫자가 존재하지 않음

순열은 순서가 매우 중요함

크기가 N인 수열은 총 N!개가 존재한다.

#### 다음순열

- 순열을 사전 순으로 나열했을 때, 사전순으로 다음에 오는 순열과 이전에 오는 순열ㅇ르 찾는 방법
- C++ STL의 algorithm에는 이미 next_permutation과 prev_permutation이 존해

**방법**

1. A[i-1] < A[i] 를 만족하는 가장 큰 i를 찾는다.
2. j>=i 이면서 A[j] > A[i-1]를 만적하는 가장 큰 j를 찾는다.
3. A[i-1] 과 A[j]를 swap한다.
4. A[i]부터 순열을 뒤집는다.

7 2 3 < 6 > 5 > 4 > 1

3이 i-1, 6이 i

3이 바뀌고 그 뒤는 첫순열이 나와야함

3보다 크면서 가장 작은수를 오른쪽에서 찾는다.(4 = 가장 오른쪽, j)

swap해서 7 2 4 6 5 3 1 이 되고

6 5 3 1을 뒤집어 오름차순으로 만들어 첫순열을 만들어주면

7 2 4 1 3 5 6 이 다음 순열이 됨

``` cpp
bool next_permutation(int *a, int n) {
  int i = n - 1;
  while(i > 0 && a[i-1] >= a[i]) i-=1;
  if(i <= 0) return false; // 마지막 순열
  int j = n - 1;
  while(a[j] <= a[i-1]) j-=1;
  swap(a[i-1], a[j]);
  j = n - 1;
  while(i < j) {
    swap(a[i], a[j]);
    i+=1; j-=1;
  }
  return true;
}
```

시간복잡도 : O(N) (O(N) + O(N) + O(1) + O(N))

#### 다음 순열 문제

[다음 순열 문제](https://www.acmicpc.net/problem/10972)

``` cpp
#include <iostream>
#include <algorithm>
#include <vector>
using namespace std;

class next_permutation_m {
public:
  bool operator()(vector<int>& p) {
    int n = p.size();
    int i = n - 1;
    while (i > 0 && p[i - 1] >= p[i]) i -= 1;
    if (i <= 0)
      return false;
    int j = n - 1;
    while (p[j] <= p[i - 1]) j -= 1;
    swap(p[i - 1], p[j]);

    j = n - 1;
    while (i < j) {
      swap(p[i], p[j]);
      i += 1;
      j -= 1;
    }
    return true;
  }
};

int main()
{
  int N = 0;
  cin >> N;
  vector<int> permutation(N, 0);
  for (int i = 0; i < N; ++i) {
    cin >> permutation[i];
  }

  next_permutation_m np;
  if (np(permutation)) {
    for (auto i = permutation.begin(); i != permutation.end(); ++i)
      cout << *i << " ";
    cout << endl;
  }
  else {
    cout << "-1" << endl;
  }
  return 0;
}
```

#### 이전 순열 문제

[이전 순열 문제](https://www.acmicpc.net/problem/10973)

``` cpp
#include <iostream>
#include <algorithm>
#include <vector>
using namespace std;

int main()
{
  int N = 0;
  cin >> N;
  vector<int> permutation(N, 0);
  for (int i = 0; i < N; ++i) {
    cin >> permutation[i];
  }

  if (prev_permutation(permutation.begin(), permutation.end())) {
    for (auto i = permutation.begin(); i != permutation.end(); ++i)
      cout << *i << " ";
    cout << endl;
  }
  else {
    cout << "-1" << endl;
  }
  return 0;
}
```

#### 모든 순열

모든 순열은 다음 순열을 계속 보면서 마지막 순열까지 돌면 구할 수 있음

#### 모든 순열 문제

[모든 순열 문제](https://www.acmicpc.net/problem/10974)

``` cpp
#include <iostream>
#include <algorithm>
#include <vector>
using namespace std;

int main()
{
  int N = 0;
  cin >> N;
  vector<int> permutation(N, 0);
  for (int i = 0; i < N; i++) {
    permutation[i] = i + 1;
  }

  do {
    for (auto i = permutation.begin(); i != permutation.end(); ++i)
      cout << *i << " ";
    cout << '\n';
  } while (next_permutation(permutation.begin(), permutation.end()));
  return 0;
}
```

10! 까지막 1초 걸림

#### 차이를 최대로 문제

[차이를 최대로 문제](https://www.acmicpc.net/problem/10819)

``` cpp
#include <iostream>
#include <vector>
#include <algorithm>
#include <math.h>

using namespace std;

int main()
{
  int N = 0;
  cin >> N;
  vector<int> permutation(N, 0);
  for (int i = 0; i < N; ++i) {
    cin >> permutation[i];
  }
  sort(permutation.begin(), permutation.end());
  int result = 0;
  do {
    int sum = 0;
    for (int i = 1; i < N; i++) {
      sum += abs(permutation[i] - permutation[i - 1]);
    }
    result = max(result, sum);
  } while (next_permutation(permutation.begin(), permutation.end()));
  cout << result << '\n';
  return 0;
}
```

#### 차이를 최대로 문제(TSP)

순열을 이용하려면 반드시 N 제한 살펴봐야함

10이하이므로 올바른 모든 경우 사용가능

[차이를 최대로 문제](https://www.acmicpc.net/problem/10971)

``` cpp
#include <iostream>
#include <algorithm>
#include <vector>
using namespace std;

class TSP {
public:
  int operator()(vector<vector<int>>& w, int n) {
    vector<int> permutation(n, 0);
    for (int i = 0; i < n; ++i) {
      permutation[i] = i;
    }
    int result = numeric_limits<int>::max();
    do {
      bool available = true;
      int cost = 0;
      for (int i = 0; i < n-1; i++) {
        int temp = w[permutation[i]][permutation[i+1]];
        if (temp == 0) {
          available = false;
          break;
        }
        else {
          cost += temp;
        }
      }
      int temp = w[permutation[n - 1]][permutation[0]];
      if (temp == 0) {
        available = false;
      }
      else {
        cost += temp;
      }
      if (available && result >= cost) {
        result = cost;
      }
    } while (next_permutation(permutation.begin(), permutation.end()));
    return result;
  }
};

int main()
{
  int N = 0;
  cin >> N;

  vector<vector<int>> weight(N, vector<int>(N,0));
  for (int i = 0; i < N; ++i) {
    for (int j = 0; j < N; ++j) {
      cin >> weight[i][j];
    }
  }

  TSP tsp;
  cout << tsp(weight, N) << endl;
  return 0;
}
```

시간복잡도 O(NXN!)

문제의 특성상 1 2 3 4, 2 3 4 1, 3 4 1 2, 4 1 2 3 에서 다시 1로 돌아오니까 모두 같은 경우이다.

-> 시작점을 1로 고정해도 된다.(O(N!)으로 해결가능)

#### 로또 문제

순열에 같은수가 있어도 잘 구함

K개의 수 중에 6개의 수를 고르는 문제

고른 것을 1 아닌 것을 0으로 하여 순열로 풀 수 있음

[로또 문제](https://www.acmicpc.net/problem/6603)

``` cpp
#include <iostream>
#include <cstdio>
#include <vector>
#include <algorithm>
using namespace std;

int main()
{
  int k = 0;
  bool first = true;
  while (1) {
    if (first) {
      first = false;
    }
    else {
      cout << endl;
    }
    cin >> k;
    if (k == 0)
      break;
    vector<int> permutation(k, 0);
    vector<int> select(k, 1);
    for (int i = 0; i < k; ++i) {
      cin >> permutation[i];
    }
    for (int i = 0; i < 6; ++i) {
      select[i] = 0;
    }
    do {
      vector<int> result(6, 0);
      int p = 0;
      for (int i = 0; i < k; ++i) {
        if (select[i] == 0) {
          result[p] = permutation[i];
          p += 1;
        }
      }
      sort(result.begin(), result.end());
      for (int i = 0; i < 6; ++i)
        cout << result[i] << ' ';
      cout << endl;
    } while (next_permutation(select.begin(), select.end()));
  }
  return 0;
}
```

#### 연산자 끼워넣기 문제

개수가 정해져있고, 순서만 바뀌는 것은 순열로 풀 수 있음(mapping 이용)

[연산자 끼워넣기 문제](https://www.acmicpc.net/problem/14888)

``` cpp
#include <iostream>
#include <vector>
#include <algorithm>
using namespace std;

int main()
{
  int N = 0;
  cin >> N;
  vector<int> arr(N, 0);
  for (int i = 0; i < N; ++i) {
    cin >> arr[i];
  }
  vector<int> oper;
  for (int i = 0; i < 4; ++i) {
    int num = 0;
    cin >> num;
    for (int j = 0; j < num; j++) {
      if (i == 0) {
        oper.push_back(0);
      }
      else if (i == 1) {
        oper.push_back(1);
      }
      else if (i == 2) {
        oper.push_back(2);
      }
      else if (i == 3) {
        oper.push_back(3);
      }
    }
  }
  int max_value = numeric_limits<int>::min();
  int min_value = numeric_limits<int>::max();
  do {
    int result = arr[0];
    for (int k = 1; k <= N-1; k++) {
      if (oper[k-1] == 0) {
        result += arr[k];
      }
      else if (oper[k-1] == 1) {
        result -= arr[k];
      }
      else if (oper[k-1] == 2) {
        result *= arr[k];
      }
      else if (oper[k-1] == 3) {
        result /= arr[k];
      }
    }
    if (result > max_value) {
      max_value = result;
    }
    if (result < min_value) {
      min_value = result;
    }
  } while (next_permutation(oper.begin(), oper.end()));
  cout << max_value << endl;
  cout << min_value << endl;
  return 0;
}
```