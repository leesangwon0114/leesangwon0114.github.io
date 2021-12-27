---
layout: post
title:  "PythonAlgorithm 1.&nbsp;파이썬 기초"
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

---

#### 리스트 컴프리헨션

- 기존 리스트를 기반으로 새로운 리스트를 만들어내는 구문

``` python
list(map(lambda x: x+10, [1,2,3])) # [11, 12, 13]

# 홀수인 경우 2를 곱해 출력하는 리스트
[n*2 for n in range(1, 10+1) if n % 2 == 1] # [2, 6, 10, 14,18]

# 리스트 컴프리헨션 사용 안하면
a = []
for n in range(1, 10+1):
    if n % 2 == 1:
        a.append(n*2)


# 딕셔너리에도 가능
a = {key:value for key, value in original.items()}

```

---

#### 제너레이터

- 루프의 반복 동작을 제어할 수 있는 루틴
- 예를 들어 숫자 1억 개를 만들어내 계산하는 프로그램이 있다고 가정
- 제너레이터가 없다면 숫자 1억 개를 메모리에 보관해야함
- 제너레이터 이용 시 생성만 해주고 필요할 때 숫자를 만들어 냄
- yield 구문을 사용하여, 실행 중이던 값을 내보낸다

``` python
def get_natural_number():
    n = 0
    while True:
        n+=1
        yield n

g = get_natural_number()

for _ in range(0, 100):
    print(next(g))

```

---

#### range

- 제너레이터의 방식을 활용하는 대표적 함수
- 파이썬 2 에서는 range는 숫자를 미리 생성해서 리턴, xrange가 제너레이터 방식
- 파이썬 3 에서 range가 제너레이터 역할을 하고 xrange 는 사라짐

``` python
# 백만개의 숫자를 출력하는 코드
a = [n for n in range(1000000)]
b = range(1000000)

# 길이는 동일한 100만 개가 출력
len(a) == len(b) # True

# 메모리 점유율을 비교
 sys.getsizeof(a) # 8697464
 sys.getsizeof(b) # 48

```

---

#### 구글 파이썬 스타일 가이드

``` python
# 함수의 기본 값으로 가변 객체(Mutable Object) 사용 금지
def foo(a, b=[]):
def foo(a, b: Mapping = {}):

# 불변 객체(Immutable Object)
def foo(a, b=None):
def foo(a, b: Optional[Sequence]=None):

# True, False 판별 시 암시적 방법 사용 권장
if not users: # Yes
if len(users) == 0: # No
if foo == 0: # Yes
if foo is not None and not foo: # No
if i % 10 == 0: # Yes
if not i % 10: # No  
```

---

#### 가변/불변 객체

- 파이썬은 모든 것이 객체이다
- immutable 객체 :  bool, int, float, tuple, str
- mutable 객체 : list, set, dict

``` python
# 참조 변수에서 값을 조작하는 경우 참조 대상의 변수도 변경할 수 있게 됨
a = [1,2,3]
b = a
b[2] = 5
print(a) # [1,2,5]

# is 는 id() 값을 비교하는 함수
# None은 값 자체가 정의되어 있지 않아 ==로 비교 불가 -> is로만 가능
if a is None:
    pass

a = [1,2,3]
a == a # True
a == list(a) # True
a is a # True
a is list(a) # False
# 값은 동일하지만 list로 한 번 더 묶어주면 별도의 객체로 복사가 되고 다른 ID를 갖게 된다.
a == copy.deepcopy(a) # True, 값은 같기 때문에
a is copy.deepcopy(a) # False, ID는 다르기 때문에
```
---