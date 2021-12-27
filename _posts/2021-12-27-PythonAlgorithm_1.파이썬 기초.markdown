---
layout: post
title:  "PythonAlgorithm;1.&nbsp;파이썬 기초"
date:   2021-12-27 02:18:15 +0700
categories: [Algorithm, python]
---

파이썬 알고리즘 인터뷰 책 정리

---

> 파이썬 기초

#### 타입힌트

- 파이썬은 대표적인 동적 타이핑 언어 
- 파이썬 3.5 부터 타입힌트 사용 가능

``` python
str = "1"
int = 1

def fn(a): # 기존 타입힌트를 사용하지 않는 파이썬 함수

def fn(a:int) -> bool: # 타입 힌트 사용
                       # a가 정수형, bool 리턴 값을 확실히 알 수 있음
```

pip install mypy 를 통해 타입힌트 오류여부 자동 확인 가능
