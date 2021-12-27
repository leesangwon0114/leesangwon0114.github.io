---
layout: post
title:  "C++Algorithm;9.&nbsp;그래프와BFS - 그래프의 탐색"
date:   2019-01-12 01:18:15 +0700
categories: [Algorithm, c++]
published : true
---

codeplus 백준 강사 강의 내용기반으로 정리한 내용입니다.(비공개 포스트)

---

> 그래프의 탐색

탐색 목적: 시작점 X 시작해서 모든 정점 1번씩 방문

#### DFS

DFS - 스택을 사용해서 최대한 가고 갈 수 없으면 이전 정점으로 돌아옴

한번씩 방문하기 위해 check 배열 필요

-> 재귀호출을 이용해서 구현

``` cpp
void dfs(int x) {
    check[x] = true;
    for(int i=1; i<=n; i++) {
        if (a[x][i] == 1 && check[i] == false) {
            dfs(i);
        }
    }
}
```

-> 인접행렬을 이용한 구현 (O(v^2))

``` cpp
void dfs(int x) {
    check[x] = true;
    for(int i=0; i<a[x].size(); i++) {
        int y = a[x][i];
        if (check[y] == false)
            dfs(y);
    }
}
```

-> 인접 리스트를 이용한 구현(O(v+e))

#### BFS

큐를 이용해서 모두 방문

-> 큐에 넣는 동시에 방문했다고 check 해주어야 함

``` cpp
queue<int> q;
check[1] = true; q.push(1);
while(!q.empty()) {
    in x = q.front(); q.pop();
    for(int i=1; i<=n; i++) {
        if (a[x][i] == 1 && check[i] == false) {
            check[i] = true;
            q.push(i);
        }
    }
}
```

-> 인접 행렬을 이용한 구현 (O(v^2))

``` cpp
queue<int> q;
check[1] = true; q.push(1);
while(!q.empty()) {
    in x = q.front(); q.pop();
    for(int i=0; i<a[x].size(); i++) {
        int y = a[x][i];
        if (check[y] == false) {
            check[y] = true; q.push(y);
        }
    }
}
```

-> 인접 리스트를 이용한 구현 (O(v+e))

#### DFS와 BFS 문제

[DFS와 BFS 문제](https://www.acmicpc.net/problem/1260)

``` cpp
#include <iostream>
#include <vector>
#include <queue>
#include <algorithm>
using namespace std;

class DFS {
  int n;
  int v;
  vector<bool> check;
  vector<int> result;
public:
  DFS(int input_n, int start, vector<vector<int>>& graph) : n(input_n), v(start) {
    check.assign(input_n+1, false);
    result.push_back(start);
    dfs(start, graph);
  }
  void dfs(int x, vector<vector<int>>& graph) {
    check[x] = true;
    for (int i = 0; i<graph[x].size(); i++) {
      int y = graph[x][i];
      if (check[y] == false) {
        result.push_back(y);
        dfs(y, graph);
      }
    }
  }

  void print() {
    for (int i = 0; i < result.size(); ++i) {
      cout << result[i] << " ";
    }
    cout << endl;
  }
};

class BFS {
  int n;
  int v;
  vector<bool> check;
  vector<int> result;
public:
  BFS(int input_n, int start, vector<vector<int>>& graph) : n(input_n), v(start) {
    check.assign(input_n + 1, false);
    result.push_back(start);
    bfs(start, graph);
  }
  void bfs(int x, vector<vector<int>>& graph) {
    queue<int> q;
    q.push(x);
    check[x] = true;
    while (!q.empty()) {
      int y = q.front(); q.pop();
      for (int i = 0; i<graph[y].size(); i++) {
        int t = graph[y][i];
        if (check[t] == false) {
          check[t] = true;
          result.push_back(t);
          q.push(t);
        }
      }
    }
  }

  void print() {
    for (int i = 0; i < result.size(); ++i) {
      cout << result[i] << " ";
    }
    cout << endl;
  }
};

int main()
{
  int N = 0;
  int M = 0;
  int V = 0;

  cin >> N >> M >> V;

  vector<vector<int>> graph(N + 1, vector<int>(0, 0));
  for (int i = 0; i < M; ++i) {
    int a = 0;
    int b = 0;
    cin >> a >> b;
    graph[a].push_back(b);
    graph[b].push_back(a);
  }

  for (int i = 1; i <= N; ++i) {
    sort(graph[i].begin(), graph[i].end());
  }

  DFS dfs(N, V, graph);
  dfs.print();

  BFS bfs(N, V, graph);
  bfs.print();
  return 0;
}
---

> 연결요소

하나의 그래프이지만 각각 나누어진 그래프를 각각 연결요소라 부름

연결요소의 개수를 구하려면 BFS나 DFS를 이용해서 구할 수 있음

-> DFS, BFS의 목적이 한 정점에서 시작해서 여결된 모든 정점을 한번씩 방문하는 것이기 때문에 가능

#### 연결요소 문제

[연결요소 문제](https://www.acmicpc.net/problem/11724)

``` cpp
#include <iostream>
#include <vector>
#include <queue>
using namespace std;

vector<bool> check;

class CC {
public:
  void operator()(int start, vector<vector<int>>& g) {
    bfs(start, g);
  }
  void bfs(int s, vector<vector<int>>& g) {
    queue<int> q;
    q.push(s);
    check[s] = true;

    while (!q.empty()) {
      int x = q.front(); q.pop();
      for (int i = 0; i < g[x].size(); ++i) {
        if (check[g[x][i]] == false) {
          check[g[x][i]] = true;
          q.push(g[x][i]);
        }
      }
    }
  }
};

int main()
{
  int N = 0;
  int M = 0;
  cin >> N >> M;
  check.assign(N + 1, false);
  vector<vector<int>> graph(N + 1, vector<int>(0, 0));
  for (int i = 0; i < M; ++i) {
    int a = 0;
    int b = 0;
    cin >> a >> b;
    graph[a].push_back(b);
    graph[b].push_back(a);
  }

  int result = 0;
  CC c;
  for (int i = 1; i <= N; ++i) {
    if (!check[i]) {
      result++;
      c(i, graph);
    }
  }
  cout << result << endl;
  return 0;
}
```

---

> 이분그래프

그래프를 A와 B로 나눌 수 있으면 이분 그래프라 함

A에 포함되어 있는 정점끼리 연결된 간선이 없음

B에 포함되어 있는 정점끼리 연결된 간선이 없음

모든 간선의 한 끝 점은 A에, 다른 끝 점은 B에

DFS 또는 BFS 탐색으로 이분그래프인지 아닌지 알 수 있다.

-> 방문한 적이 있는 정점에 대해 색을 검사해야함

#### 이분그래프 문제

3-c 코드 -> 1이면 2가되고 2면 1이됨

[이분그래프 문제](https://www.acmicpc.net/problem/1707)

``` cpp
#include <iostream>
#include <vector>
using namespace std;

class BipartiteGraph {
  vector<bool> check;
  vector<int> color;
  bool result = true;
public:
  void dfs(int start, int c, vector<vector<int>>& g) {
    check[start] = true;
    color[start] = c;
    for (int i = 0; i < g[start].size(); ++i) {
      if (color[g[start][i]] == 0) {
        if (check[g[start][i]] == false) {
            dfs(g[start][i], 3 - c, g);
        }
      }
      else if (color[start] == color[g[start][i]]) {
        result = false;
      }
    }
  }
  void operator()(int v, vector<vector<int>>& g) {
    check.assign(v+1, false);
    color.assign(v+1, 0);

    for (int i = 1; i <= v; ++i) {
      if (color[i] == 0)
        dfs(i, 1, g);
    }

    if (result)
      cout << "YES" << endl;
    else
      cout << "NO" << endl;
  }
};

int main()
{
  int testcase = 0;
  cin >> testcase;
  while (testcase--) {
    int V = 0;
    int E = 0;

    cin >> V >> E;

    vector<vector<int>> graph(V + 1, vector<int>(0, 0));

    for (int i = 0; i < E; ++i) {
      int a = 0;
      int b = 0;
      cin >> a >> b;
      graph[a].push_back(b);
      graph[b].push_back(a);
    }

    BipartiteGraph bg;
    bg(V, graph);
  }
  return 0;
}
```
