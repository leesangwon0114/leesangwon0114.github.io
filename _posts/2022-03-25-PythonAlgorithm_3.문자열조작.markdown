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

---

#### Group Anagrams 문제

[Group Anagrams 문제](https://leetcode.com/problems/group-anagrams/)

``` python
class Solution(object):
    def groupAnagrams(self, strs):
        """
        :type strs: List[str]
        :rtype: List[List[str]]
        """
        d = {}
        for s in strs:
            ordered_str = ''.join(sorted(s))    
            if ordered_str not in d:
                d[ordered_str] = []
            d[ordered_str].append(s)
        ' '.join(sorted(d, key=lambda k: len(d[k]), reverse=True))
        r = []
        for k, v in d.items():
            r.append(v)
        return r
```

더 깔끔하게 코딩하려면 defaultdict()를 선언하여 만들면 빈 dict 에 삽입 시 keyerror 가 발생하지 않음

anagrams = collections.defaultdict(list)

-> default 가 빈 리스트인 dictionary

파이썬은 정렬 시 sorted()함수를 사용하면 팀소트 사용하여 고성능 정렬이 가능함

sorted 함수는 정렬된 리스트를 리턴하므로 ''.join(sorted(str))을 통해 정렬된 문자열을 얻을 수 있음

---

#### 가장긴 패닌드롬 부분문자열 문제

[가장긴 패닌드롬 부분문자열 문제](https://leetcode.com/problems/longest-palindromic-substring/)

2칸, 3칸짜리 포인터가 왼쪽에서 부터 슬라이딩 윈도우 처럼 중앙까지 전진

중앙을 중심으로 확장하여 홀수(3칸), 짝수(2칸) 중 가장 긴 값이 저장됨

![Alt text](http://leesangwon0114.github.io/static/img/ROS2/3.1.PNG)


``` python
class Solution(object):
    def longestPalindrome(self, s):
        """
        :type s: str
        :rtype: str
        """
        def expand(left, right):
            # 중앙에서 부터 두 포인트 확장
            while(left >=0 and right < len(s) and s[left] == s[right]):
                left -= 1
                right += 1
            return s[left+1:right]
        
        # Palindrome 해당사항 없을 때 즉시 리턴
        if len(s) < 2 or s == s[::-1]:
            return s
        
        result = ''
        for i in range(0, len(s)-1):
            result = max(result, expand(i, i+1), expand(i, i+2), key=len)
        return result
```

---