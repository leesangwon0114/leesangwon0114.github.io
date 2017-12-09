---
layout: post
title:  "PatternRecognition&nbsp;3.&nbsp;LDA"
date:   2017-12-08 22:40:15 +0700
categories: [PatternRecognition]
---

### LDA와 PCA

LDA - 클래스의 정보를 보호하면서 두 클래스를 포함하는 data들을 가장 잘 감소시킬 수 있는 축을 찾는 것이 목적(두 그룹의 분리를 잘 할 수 있는 방법을 찾기 위함)

PCA - 차원감소를 주목적으로 하는 방법으로 전체적인 data의 분포를 보았을 때 주요한 축으로 data를 project 하는 방법(데이터의 전체적인 분포를 최대한 유지하기 위함)

![Alt text](http://leesangwon0114.github.io/static/img/PR/3.1.png)


### LDA(Linear Discriminant Analysis)

Dimensionality reduction 만을 위한 방법이 아님

여러 클래스가 존재할 때 그 클래스들을 최대한 잘 분리시키는 projection을 차는 것이 목적

projection 시킨 데이터들에서 같은 클래스에 속하는 데이터들의 variance는 최대한 줄이고, 각 데이터들의 평균 값들의 variance는 최대한 키워서 클래스들끼리 최대한 멀리 떨어지게 만드는 것

![Alt text](http://leesangwon0114.github.io/static/img/PR/3.2.png)

