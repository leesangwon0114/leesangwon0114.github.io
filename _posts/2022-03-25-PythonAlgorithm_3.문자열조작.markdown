---
layout: post
title:  "PythonAlgorithm 3.&nbsp;문자열조작"
date:   2022-03-25 04:18:15 +0700
categories: [Algorithm, python]
---

파이썬 알고리즘 인터뷰 책 정리

---

#### Valid Palindrome 문제

[Valid Palindrome 문제](https://leetcode.com/problems/valid-palindrome/)

isalnum() 함수를 통해 영숫자 여부를 판단할 수 있지만 아래와 같이 정규식으로도 판단 가능

``` python
 re.sub('[^a-z0-9]', '', string)
```

[::-1] 슬라이싱을 통해 문자열 뒤집기 활용

``` python
class Solution:
    def isPalindrome(self, s: str) -> bool:
        s = s.lower()
        temp = [c for c in s if c.isalnum()]
        return temp == temp[::-1]
```

---

#### 문자열 뒤집기 문제

[문자열 뒤집기 문제](https://leetcode.com/problems/reverse-string/)

문자열 슬라이싱은 내부적으로 메모리 할당하여 통과할 수 없음

리스트 이므로 reverse() 함수를 쓰면 간단히 해결 가능

``` python
class Solution:
    def reverseString(self, s: List[str]) -> None:
        """
        Do not return anything, modify s in-place instead.
        """
        s.reverse()
```

그러나 전통적인 투포인트 활용 방식도 구현해봄

``` python
class Solution:
    def reverseString(self, s: List[str]) -> None:
        """
        Do not return anything, modify s in-place instead.
        """
        # s.reverse()
        left, right = 0, len(s) -1

        while(left < right):
            s[left], s[right] = s[right], s[left]
            left+=1
            right-=1
```

---

#### Reorder Data in Log Files 문제

[Reorder Data in Log Files 문제](https://leetcode.com/problems/reorder-data-in-log-files/)


``` python
class Solution:
    def reorderLogFiles(self, logs: List[str]) -> List[str]:
        
```