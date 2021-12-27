---
layout: post
title:  "C++Algorithm;5.&nbsp;브루트포스 - 재귀함수사용"
date:   2019-01-02 02:18:15 +0700
categories: [Algorithm, c++]
---

codeplus 백준 강사 강의 내용기반으로 정리한 내용입니다.(비공개 포스트)

---

> 재귀 함수 사용

#### 1,2,3 더하기 문제

[1,2,3 더하기 문제](https://www.acmicpc.net/problem/9095)

``` cpp
#include <iostream>
using namespace std;

class go {
public:
  int solve(int count, int sum, int goal) {
    if (sum > goal) return 0;
    if (sum == goal) return 1;

    int cur = 0;
    for (int i = 1; i <= 3; i++) {
      cur += solve(count + 1, sum + i, goal);
    }
    return cur;
  }
  int operator()(int goal) {
    return solve(0, 0, goal);
  }
};

int main()
{
  int T = 0;
  cin >> T;
  while (T--) {
    int n = 0;
    cin >> n;
    go g;
    cout << g(n) << endl;
  }
}
```

시간복잡도 O(3^n)

#### 암호 만들기 문제

재귀함수

1. 불가능한 경우
2. 정답을 찾은 경우
3. 다음 경우 호출

-> 1, 2 순서 잘 고려하기!

[암호 만들기 문제](https://www.acmicpc.net/problem/1759)

``` cpp
#include <iostream>
#include <vector>
#include <string>
#include <algorithm>
using namespace std;

class Secret {
public:
  bool check(string& password) {
    int mo = 0;
    int za = 0;

    for (char p : password) {
      if (p == 'a' || p == 'e' || p == 'i' || p == 'o' || p == 'u')
        mo++;
      else
        za++;
    }
    return mo >= 1 && za >= 2;
  }
  void make(int n, vector<char>& alpha, string password, int i) {
    if (password.length() == n) {
      if (check(password))
        cout << password << '\n';
      return;
    }
    if (i < alpha.size()) {
      make(n, alpha, password + alpha[i], i + 1);
      make(n, alpha, password, i + 1);
    }
  }
  void operator()(int n, vector<char>& avail_alpha) {
    make(n, avail_alpha, "", 0);
  }
};

int main()
{
  int L = 0;
  int C = 0;

  cin >> L >> C;

  vector<char> alpha(C, 0);
  for (int i = 0; i < C; ++i)
    cin >> alpha[i];
  sort(alpha.begin(), alpha.end());
  Secret secret;
  secret(L, alpha);
  return 0;
}
```

#### 로또 문제

재귀함수로 풀기

[로또 문제](https://www.acmicpc.net/problem/6603)

``` cpp
#include <iostream>
#include <vector>
using namespace std;

class Lotto {
  vector<int> sol;
public:
  void solve(int cnt, vector<int>& num, int i) {
    if (cnt == 6) {
      for (int i = 0; i < 6; ++i) {
        cout << sol[i] << " ";
      }
      cout << endl;
      return;
    }
    if (i == num.size()) return;
    sol.push_back(num[i]);
    solve(cnt + 1, num, i + 1);
    sol.pop_back();
    solve(cnt, num, i + 1);
  }
  void operator()(int n, vector<int>& num) {
    solve(0, num, 0);
  }
};

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
    vector<int> input(k, 0);
    for (int i = 0; i < k; ++i) {
      cin >> input[i];
    }
    Lotto lotto;
    lotto(k, input);
  }
  return 0;
}
```

#### 부분집합의 합 문제

[부분집합의 합 문제](https://www.acmicpc.net/problem/1182)

``` cpp
#include <iostream>
#include <vector>
#include <algorithm>
using namespace std;

class Set {
  int ans = 0;
public:
  void solve(int s, vector<int>& element, int cursum, int index) {
    if (index == element.size()) {
      if (cursum == s)
        ans += 1;
      return;
    }
    solve(s, element, cursum + element[index], index + 1);
    solve(s, element, cursum, index + 1);
  }
  int get_answer() {
    return ans;
  }
  void operator()(int s, vector<int>& element) {
    solve(s, element, 0, 0);
  }
};

int main()
{
  int N = 0;
  int S = 0;
  cin >> N >> S;

  vector<int> arr(N,0);
  for (int i = 0; i < N; ++i) {
    cin >> arr[i];
  }

  Set set;
  set(S, arr);
  int result = set.get_answer();
  if (S == 0) result -= 1;
  cout << result << endl;
  return 0;
}
```

-> index가 지나가더라도 전체 다보기 전까지 끝나면 안됨

#### 퇴사 문제

[퇴사 문제](https://www.acmicpc.net/problem/14501)

``` cpp
#include <iostream>
#include <vector>
#include <algorithm>
using namespace std;

struct consult {
  int T;
  int P;
  consult(int t, int p) {
    T = t;
    P = p;
  }
};

class Decision {
  int n = 0;
  int result = numeric_limits<int>::min();
public:
  void solve(int day, int sum, vector<consult>& input) {
    if (day > n + 1) return;
    if (day == n + 1) {
      if (result < sum)
        result = sum;
      return;
    }
    solve(day + 1, sum, input);
    solve(day + input[day].T, sum + input[day].P, input);
  }
  void operator()(int N, vector<consult>& input) {
    n = N;
    solve(1, 0, input);
  }
  void print() {
    cout << result << endl;
  }
};

int main() {
  int N = 0;
  cin >> N;

  vector<consult> input(N+1, consult(0, 0));

  for (int i = 1; i <= N; ++i) {
    cin >> input[i].T;
    cin >> input[i].P;
  }

  Decision decision;
  decision(N, input);
  decision.print();
  return 0;
}
```
