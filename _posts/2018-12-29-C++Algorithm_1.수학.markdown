---
layout: post
title:  "C++Algorithm 1.&nbsp;수학"
date:   2018-12-29 02:18:15 +0700
categories: [Algorithm, c++]
---

codeplus 백준 강사 강의 내용기반으로 정리한 내용입니다.(비공개 포스트)

---

> 나머지

#### 나머지 연산(Modular Arithmetic)

- 컴퓨터의 정수는 저장할 수 있는 범위가 한정되어 있기 때문에 답을 M으로 나눈 나머지를 출력하라는 문제가 등장(정답을 구한 후 나누는 것이아니라 중간과정에서 모두 Modular를 취하면서 계산해야함)
- (A+B) mod M = ((A mod M) + (B mod M)) mod M
- (AXB) mod M = ((A mod M) X (B mod M)) mod M
- 나누기의 경우 성립하지 않는다: (6/3)%3 = 2
- 나누기는 페르마의 소정리에 의해 (A/B) % C = (AXB^C-2) % C 와 같다.(단, C는 소수이며, A, B는 서로소여야 함)
- 뺄셈의 경우에는 먼저 mod 연산을 한 결과가 음수가 나올 수 있기 때문에 아래와 같이한다.
- (A-B) mod M = ((A mod M) - (B mod M) + M) mod M

#### 나머지 문제

[나머지 문제](https://www.acmicpc.net/problem/10430)

- 첫째 줄에 (A+B)%C
- 둘째 줄에 (A%C + B%C)%C
- 셋째 줄에 (AXB) % C
- 넷째 줄에 (A%C X B%C) % C

를 출력하는 문제

첫째 줄과 둘째 줄, 셋째 줄과 넷째 줄의 결과가 같다.

``` cpp
#include <iostream>
using namespace std;

int main()
{
  int A = 0;
  int B = 0;
  int C = 0;

  cin >> A >> B >> C;

  cout << (A + B) % C << endl;
  cout << (A%C + B % C) % C << endl;
  cout << (A*B) % C << endl;
  cout << (A%C * B%C) % C << endl;

  return 0;
}
```

---

> 최대공약수와 최소공배수

정답이 분수형태일 때 기약분수를 구하는 경우 사용

나머지는 수학공식 사용

#### Greatest Common Divisor(최대공약수)

- 최대공약수는 줄여서 GCD라고 쓴다.
- 약수 : N을 나눌 수 있는 수
- 공약수 : 두 수의 약수 중에서 공통된 약수
- 두 수 A와 B의 최대공약수 G는 A와 B의 공통된 약수 중에서 가장 큰 정수

``` cpp
int g = 1;
for (int i=2; i<=min(a, b); i++)
{
    if (a%i == 0 && b%i == 0) {
        g = i;
    }
}
```

i가 커지니까 비교할 필요 없음

if 문에 들어가면 이전보다 항상 큰 값이 들어감

시간복잡도는 O(N)

더 빠른 방법은 유클리드 호제법

#### 유클리드 호제법

- GCD(a, b) == GCD(b, a%b)
- a%b가 0dlaus 그 때 b가 최대 공약수 이다.
- GCD(24, 16) = GCD(16, 8) = GCD(8,0) = 8

-> 재귀함수를 사용해서 구현한 유클리드 호제밥

``` cpp
int gcd(int a, int b) {
    if (b==0) {
        return a;
    } else {
        gcd(b, a%b);
    }
}
```

위의 코드 시간복잡도는 O(lgN)

재귀함수를 사용하지 않고 구현한 유클리드 호제법

``` cpp
int gcd(int a, int b) {
    while(b!=0) {
        int r = a%b;
        a = b;
        b = r;
    }
    return a;
}
```

위의 코드도 시간복잡도는 O(lgN)

#### 세수의 최대공약수

- GCD(a, b, c) = GCD(GCD(a, b), c)

#### 최소공배수

- 최소공배수는 줄여서 LCM이라고 한다.
- 최대공약수를 응용해서 구할 수 있음
- a,b 의 최대공약수를 g라고 했을 때 최소공배수 l = g*(a/g)*(b/g)

#### 최대공약수와 최소공배수 문제

[최대공약수와 최소공배수 문제](https://www.acmicpc.net/problem/2609)

``` cpp
#include <iostream>
using namespace std;

class gcd {
public:
  int operator ()(int a, int b) {
    while (b != 0) {
      if (b == 0) {
        return a;
      }
      int mod = a % b;
      a = b;
      b = mod;
    }
    return a;
  }
};

class lcm {
public:
  int operator ()(int a, int b, int gcd) {
    return gcd * (a / gcd)*(b / gcd);
  }
};

int main()
{
  int a = 0;
  int b = 0;
  gcd gcd;
  lcm lcm;
  cin >> a >> b;

  int g = gcd(a, b);
  cout << g << endl;
  cout << lcm(a, b, g) << endl;
  return 0;
}
```

#### 최소공배수 문제

[최소공배수 문제](https://www.acmicpc.net/problem/1934)

``` cpp
#include <iostream>
using namespace std;

class gcd {
public:
  int operator ()(int a, int b) {
    while (b != 0) {
      if (b == 0) {
        return a;
      }
      int mod = a % b;
      a = b;
      b = mod;
    }
    return a;
  }
};

class lcm {
public:
  int operator ()(int a, int b, int gcd) {
    return gcd * (a / gcd)*(b / gcd);
  }
};

int main()
{
  int T = 0;
  int A = 0;
  int B = 0;
  gcd gcd;
  lcm lcm;

  cin >> T;
  while (T--) {
    cin >> A >> B;
    cout << lcm(A, B, gcd(A, B)) << endl;
  }
  return 0;
}
```

#### GCD합 문제

[GCD합 문제](https://www.acmicpc.net/problem/9613)

``` cpp
#include <iostream>
#include <vector>
using namespace std;

class gcd {
public:
  int operator ()(int a, int b) {
    while (b != 0) {
      if (b == 0) {
        return a;
      }
      int mod = a % b;
      a = b;
      b = mod;
    }
    return a;
  }
};

int main()
{
  int t = 0;
  gcd gcd;
  cin >> t;
  while (t--) {
    int n = 0;
    cin >> n;
    vector<int> arr(n,0);
    for (int i = 0; i < n; ++i)
      cin >> arr[i];

    long long result = 0;
    for (int i = 0; i < n - 1; ++i) {
      for (int j = i + 1; j < n; ++j) {
        result += gcd(arr[i], arr[j]);
      }
    }
    cout << result << endl;
  }
  return 0;
}
```

-> result 값 long long 타입으로 해주어야함

---

> 소수

- 약수가 1과 자기 자신 밖에 없는 수
- N이 소수가 되려면, 2보다 크거나 같고, N-1보다 작거나 같은 자연수에 나누어 떨어지면 안된다.

#### 두 가지 알고리즘

1. 어떤 수 N이 소수인지 아닌지 판별하는 방법
2. N보다 작거나 같은 모든 자연수 중에서 소수를 찾아내는 방법

> 어떤 수 N이 소수인지 아닌지 판별하는 방법

``` cpp
bool prime(int n) {
    if (n < 2) {
        return false;
    }
    for (int i = 2; i <= n-1; i++) {
        if (n % i == 0) {
            return false;
        }
    }
    return true;
}
```

시간복잡도가 O(N)

#### 2보다 크거나 같고, N/2보다 작거나 같은 자연수로 나누어 떨어지면 안되는 이유

N의 약수 중에서 가장 큰 것은 N/2 보다 작거나 같기 때문

어떤 수가 소수가 아니면 N = a X b (두 자연수로 나누어 떨어져야함)

a가 가장작은 경우는 2이고 이때 b는 N/2임

따라서 n이 소수이면 2부터 N/2까지 나누어 떨어지지 않으면 됨

``` cpp
bool prime(int n) {
    if (n < 2) {
        return false;
    }
    for (int i = 2; i <= n/2; i++) {
        if (n % i == 0) {
            return false;
        }
    }
    return true;
}
```

시간복잡도는 O(N/2)인데 그냥 O(N)임

#### 2보다 크거나 같고, 루트 N 보다 작거나 같은 자연수로 나누어 떨어지면 안되는 이유

N이 소수가 아니라면 두수의 관계 aXb로 나타낼 수 있음

N = a X b 일 때 a<=b 를 모두 만족하려면 a <= 루트 N, b >= 루트 N 여야함

a < 루트N, b < 루트N 일 때 aXb < 루트N X 루트N  -> N(aXb) < N이므로 모순

a > 루트N, b > 루트N 일 때 aXb > 루트N X 루트N  -> N(aXb) > N이므로 모순

따라서 a <= 루트N, b >= 루트N 여야함

루트 N 이전만 검사하면 반대쪽 검사와 같은 결과가 나옴

루트 24 는 4.xxx (1,2,3,4 | 6,8,12,24)

``` cpp
bool prime(int n) {
    if (n < 2) {
        return false;
    }
    for (int i = 2; i*i <= n; i++) {
        if (n % i == 0) {
            return false;
        }
    }
    return true;
}
```

시간복잡도는 O(루트N) 으로 가장 빠름

어떤 수 N이 소수인지 아닌지 판별하는데 걸리는 시간복잡도 : O(루트N)

#### 소수찾기 문제

[소수찾기 문제](https://www.acmicpc.net/problem/1978)

``` cpp
#include <iostream>
using namespace std;

class prime {
public:
  bool operator()(int n) {
    if (n < 2) {
      return false;
    } else {
      for (int i = 2; i*i <= n; ++i) {
        if (n%i == 0) {
          return false;
        }
      }
      return true;
    }
  }
};

int main()
{
  int N;
  cin >> N;
  int result = 0;
  int temp = 0;
  prime prime;
  for (int i = 0; i < N; ++i) {
    cin >> temp;
    if (prime(temp))
      result++;
  }
  cout << result << endl;
}
```

> N보다 작거나 같은 모든 자연수 중에서 소수를 찾아내는 방법

루트보다 log가 시간을 훨씬 작게 만들어줌

범위 안에 들어가는 모든 소수를 구하려면 에라토스테네스의 체를 사용함

- 지워지지 않은 수 중에서 가장 작은 수는 2이다.(1은 지워버림)
- 2는 소수이고 2의 배수를 모두 지운다.
- 3의 배수를 지운다.
- 5의 배수를 지운다.
- 7의 배수를 지운다.
- 지워지지 않은 수 중 가장 작은 수가 소수이면 해당 배수를 모두 지우는 방법

``` cpp
int prime[100]; // 소수 저장
int pn = 0; // 소수의 개수
bool check[101]; // 지워졌으면 true
int n = 100; // 100까지 소수

for (int i=2; i<=n; i++) {
    if (check[i] == false) {
        prime[pn++] = i;
        for (int j = i*i; j<=n; j+=i) { // 3X2는 이미 2X3에서 지워져있음(i*i)부터~
            check[j] = true;
        }
    }
}
```

1부터 N까지 모든 소수를 구하는 것이 목표이기 때문에 바깥 for문 (i)를 N까지 돌린다.

안쪽 for문 (j)는 N의 크기에 따라서, i+i 또는 i*2로 바꾸는 것이 좋다(i가 백만인 경우 i*i는 범위를 넘어가기 때문)

시간복잡도가 O(N*loglogN) - prime harmonic series(loglogN 이 나옴)

#### 소수구하기 문제

[소수구하기 문제](https://www.acmicpc.net/problem/1929)

``` cpp
#include <iostream>
#include <vector>
using namespace std;

class primecheck {
  const int MAX = 1000000;
  vector<bool> check;
public:
  primecheck() {
    check.assign(MAX + 1, false);
    check[0] = check[1] = true;
    for (int i = 2; i*i <= MAX; i++) {
      if (check[i] == false) {
        for (int j = i + i; j <= MAX; j += i) {
          check[j] = true;
        }
      }
    }
  }
  void solve(int m, int n) {
    for (int i = m; i <= n; ++i) {
      if (check[i] == false) {
          cout << i << '\n'; // endl timeout...
      }
    }
  }
};

int main()
{
  int M = 0;
  int N = 0;
  primecheck checker;
  cin >> M >> N;
  checker.solve(M, N);
  return 0;
}
```

-> 많은 양을 출력해야할 때 endl쓰면 timeout -> \n으로 변경해서 사용하기!

#### 골드바흐의 추측

- 2보다 큰 모든 짝수는 두 소수의 합으로 표현 가능하다.
- 5보다 큰 모든 홀수는 세 소수의 합으로 표현 가능하다.
- 아직 증명되지 않은 문제이지만 10^18이하에서는 참

#### 골드바흐의 추측 문제

- 백만 이하의 짝수에 대해서 골드 바흐의 추측을 검증하는 문제
- 먼저 소수를 다 구하고 검증하면 됨

[골드바흐의 추측 문제](https://www.acmicpc.net/problem/6588)

``` cpp
#include <iostream>
#include <cstdio>
#include <vector>
using namespace std;

const int MAX = 1000000;
int prime[MAX];
int pn;
bool check[MAX + 1];

class primecheck {
public:
  primecheck() {
    check[0] = check[1] = true;
    for (int i = 2; i <= MAX; i++) {
      if (check[i] == false) {
        prime[pn++] = i;
        for (int j = i + i; j <= MAX; j += i) {
          check[j] = true;
        }
      }
    }
  }
  void solve(int n) {
    if (n < 4) {
      printf("Goldbach's conjecture is wrong.\n");
      return;
    }
    for (int i = 1; i < pn; i++) {
      if (check[n - prime[i]] == false) {
        printf("%d = %d + %d\n", n, prime[i], n-prime[i]);
        return;
      }
    }
    printf("Goldbach's conjecture is wrong.\n");
  }
};
int main()
{
  int n = 0;
  primecheck checker;
  while (1) {
    scanf("%d", &n);
    if (n == 0)
      break;
    checker.solve(n);
  }
  return 0;
}
```

-> cin, cout 너무 느림... printf, scanf 로 변경해야함