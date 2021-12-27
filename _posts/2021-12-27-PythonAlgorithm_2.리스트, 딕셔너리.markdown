---
layout: post
title:  "PythonAlgorithm 2.&nbsp;리스트, 딕셔너리"
date:   2021-12-27 04:18:15 +0700
categories: [Algorithm, python]
---

파이썬 알고리즘 인터뷰 책 정리

---

> 리스트, 딕셔너리

#### 리스트

- 순서대로 저장하는 시퀀스이자 Mutable 객체
- 입력 순서가 유지되며, 내부적으로는 동적 배열로 구현됨

---

#### 리스트 주요연산 시간 복잡도

![Alt text](http://leesangwon0114.github.io/static/img/Algorithm/python/2.1.png)

---

#### 리스트의 활용 방법

``` python
# 리스트 선언
a = list()
a = []

a = [1,2,3]
a.append(4) # [1,2,3,4]
a.insert(3,5) # 3번째 인덱스에 5을 삽입한다.
              # [1,2,3,5,4]

a[9] # 존재하지 않는 인덱스 조회 시 IndexError 
# Index Error 예외 처리
try:
    print(a[9])
except IndexError:
    print('존재하지 않는 인덱스')

# 리스트 삭제
del a[1] # index 기반 삭제
a.remove(3) #  3이라는 값 삭제
a.pop(3) # index 3번째 값 반환하고 삭제


```
파이썬의 모든 자료형은 객체로 되어 있어 리스트 안에 자료형이 각각이여도 단일 리스트에서 모두 포인터로 연결 가능

---
### 딕셔너리

- 키/값 구조로 이뤄진 자료구죠
- 입력 순서가 유지되며, 내부적으로 해시 테이블로 구현됨
- 파이썬 3.6이하에서 입력 순서가 유지되지 않아 collections.OrderedDict()라는 별도 자료형 제공
- 조회 시 항상 디폴트 값을 생성해 키 오류를 방지하는 collections.defaultdict()
- 요소의 값을 키로하고 개수를 값 형태로 만들어 카운팅하는 collections.Counter()

---

#### 딕셔너리 주요연산 시간 복잡도

![Alt text](http://leesangwon0114.github.io/static/img/Algorithm/python/2.2.png)

---

#### 딕셔너리의 활용 방법

``` python
# 딕셔너리 선언
a = dict()
a = {}

# 딕셔너리 선언 & 초기화
a = {'key1' : 'value1', 'key2' : 'value2'}
# 딕셔너리 새로운 값 할당
a['key3'] = 'value3'

# 키 에러 예외 처리
try:
    print(a['key4'])
except KeyError:
    print('존재하지 않는 키')


# 예외처리 대신 키 존재여부 미리 확인 후 작업
'key4' in a # False

# for 반복문으로 조회
for k, v in a.items():
    print(k,v)

# key 삭제
del a['key1']
```

---

#### 딕셔너리 모듈

defaultdict, Counter, OrderedDict 객체

``` python
# defaultdict
# 존재하지 않는 키 조회 시 에러 메시지 대신 디폴트 값을 기준으로 해당키에 대한 딕셔너리 아이템을 생성
a = collections.defaultdict(int)
a['A'] = 5
a['B'] = 4
# {'A':5, 'B': 4}
a['C'] +=1
# {'A':5, 'B': 4, 'C':1}
# 디폴트 0 기준

# Counter 객체
# 아이템에 대한 개수를 계산해 딕셔너리로 리턴
a = [1,5,5,5,6,6]
b = collections.Counter(a)
# {5:3, 6:2, 1:1}
b.most_common(2)
# 가장 빈도가 높은 2개의 요소 추출
# [(5,3), (6,2)]

```