---
layout: post
title:  "InformationRetrieval&nbsp;2.&nbsp;Term Vocabulary & Posting Lists"
date:   2017-12-03 21:28:15 +0700
categories: [InformationRetrieval]
---
# Document
---------------------------------------
### Parsing a Document
문서 각각의 언어 및 형식을 다루어야함

형식 -> pdf, word, excel etc.

언어는 무엇인지, character set은 유니코드인지 멀티 바이트인지 등 classification 문제가 존재

대안으로 휴리스틱 이용 

### Format/Language : Complications

하나의 index가 여러 언어를 포함할 수 있음

- Spanish pdf 가 붙여진 French email

XML, 파일 그룹?

여러 다른 포맷, 언어들을 다루기 복잡함

# Terms
---------------------------------------
### Definitions
Words - text에 나온 string

Term - normalized 된 word(upper case, lower case 제거, 형태학적으로 같은 클래스에 속한 word들을 대표 word로 변경), distinct 한 단어

Token - document 에서 나오는 word or term들의 instance, mulitple times 등장 가능

Type - 보통 term 과 같으며, tokens의 class와 동일함

**Token vs Type**

The term "token" refers to the total number of words in a text, corpus etc, regardless of how often they are repeated. The term "type" refers to the number of distinct words in a text, corpus etc. 

"a good wine is a wine that you like" contains nine tokens, but only seven types, as "a" and "wine" are repeated. 


### Normalization

text와 query에 normalize할 필요가 있음

EX. We wnat to match U.S.A. and USA

대부분 경우 terms 의 동등한 클래스를 암시적으로 정의함

window -> window, windows
windows -> Windows, windows
Windows

powerful하지만 효용성이 적음


같은 MIT 이지만 다른 뜻을 가짐(언어가 다름에 따라)
PETER WILL NIGHT MIT
He got his PhD from MIT

### Tokenization problems: One word or two?

- Hewlett-Packard
- co-education
- San Francisco

**위의 단어들을 하나로 볼지 둘로 볼지?**


**Numbers**

- 3/20/91
- Mar 20, 1991


**Chinese에는 공백이 없어 어떻게 tokenize 할 것인지?**

**Arabic은 양쪽 방향으로 읽음(Bidirectionality)**

**Case folding**

모든 단어를 lower case로?

**Stop words**

자주 사용되는 단어들에 대한 처리 

ex) a, an, and, are....

**More equivalence Classing**

의미적으러 같은 단어(car = automobile)

**Lemmatization(분류정리)**

변형된 형태를 base 형태로 바꾸어줌

ex) am, are, is -> be

ex) car, cars, car's -> car

문장속에서 다양한 형태로 활용된 단어의 표제어(lemma)를 찾는 일

> 표제어란 ? 사전에서 단어의 뜻을 찾을 때 쓰는 기본형

아름다운 이 Lemmatization을 거치면 아름답다가 된다.


**Stemming**

뒤의 단어를 제거해버리는 heuristic한 프로세스

ex) automate, automatic, automation 은 automat가 됨

![Alt text](http://leesangwon0114.github.io/static/img/IR/2.1.png)


### Porter algorithm

영어에서 일반적으로 잘 알려진 어근 추출 방

단어의 접미사를 제거하거나 다른 단어로 대치하는 역할을 하는 알고리즘

- cats → cat
- caresses → caress
- conflated → conflate
- troubling → trouble
- relational → relate

A few rules

![Alt text](http://leesangwon0114.github.io/static/img/IR/2.2.png)

### Does stemming improve effectiveness?

일반적으로 stemming은 어떤 query에서는 effectiveness 하지만 다른 것에서는 effectiveness를 감소시킬 수 있음

'oper' class는 operate, operating, operates, operation, operative 등 을 모두 포함할 것임

# Skip pointers
---------------------------------------
