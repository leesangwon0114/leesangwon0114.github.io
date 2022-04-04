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

식별자를 제외한 문자열 [1:] 을 키로 정렬하며 동일한 경우 후순위로 식별자 [0]를 지정해 정렬

배열 + 를 이용해 합침

``` python
class Solution:
    def reorderLogFiles(self, logs: List[str]) -> List[str]:
        letters, digits = [], []
        for log in logs:
            if log.split()[1].isdigit():
                digits.append(log)
            else:
                letters.append(log)
        
        letters.sort(key=lambda x: (x.split()[1:], x.split()[0]))
        return letters + digits
```

s = ['2 A', '1 B', '1 A'] 인 경우
sorted(s)
-> ['1 A', '1 B', '2 A'] 로 정렬됨

s.sort(key=lambda x:(x.split()[1], x.split()[0]))
-> ['1 A', '2 A', '1 B'] 로 정렬됨

---

#### Most Common Word 문제

[Most Common Word 문제](https://leetcode.com/problems/most-common-word/)

``` python
import re
import collections
paragraph = "Bob hit a ball, the hit BALL flew far after it was hit."
banned = ["hit"]

class Solution(object):
    def mostCommonWord(self, paragraph, banned):
        """
        :type paragraph: str
        :type banned: List[str]
        :rtype: str
        """
        words = [word for word in re.sub(r'[^a-zA-Z]', ' ', paragraph).lower().split() if word not in banned]
        counts = collections.Counter(words)
        return counts.most_common(1)[0][0]

s = Solution()
s.mostCommonWord(paragraph, banned)
```