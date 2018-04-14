---
layout: post
title:  "Computer Architecture&nbsp;2.&nbsp;Fundamentals and Trends(2/2)"
date:   2018-04-14 01:28:15 +0700
categories: [ComputerArchitecture]
---

앞에서 frequency case 에 리소스를 투입하기 위해 어떤 부분이 frequency case 인지 암달스 Law 가 잘 설명해주는 법칙임 

Performance(Cost) 를 애기할 때
X가 Y보다 n 배 더 빠르다의 의미는 아래 식과 같다

![Alt text](http://leesangwon0114.github.io/static/img/CA/1.12.PNG)

---

#### Amdahl's Law

CPU가 Int 부분, Float, 다른 Unit 부분 3가지로 나눠진다고 가정

![Alt text](http://leesangwon0114.github.io/static/img/CA/1.13.PNG)

초록색 (Float 부분) 을 2배 향상 시켰지만 전체적인 성능 부분은 오른쪽 크기 정도만 개선 됨, 즉 전체적인 성능개선의 Impact를 계산하는 것이 Amdahl's law 핵심

![Alt text](http://leesangwon0114.github.io/static/img/CA/1.14.PNG)

위의 첫번째 식에서 1-Fration(enhanced)는 영향을 받지 않는 부분(초록색을 제외한 나머지 부분을 의미)

따라서 Speedup 전체 식은 위와 같음

**Example**

![Alt text](http://leesangwon0114.github.io/static/img/CA/1.15.PNG)

Floating point 부분을 2배 개선했지만 전체 Sppedup은 1.053배 밖에 안된다는 의미(개선이 거의 안됨)

---

## CPU 부터 설계 들어감 ##

#### Factors of CPU Performance

프로그램을 구동할 때 cpu 시간은 program 당 몇초 인지로 표현 가능

CPU time = Seconds / Program

이 수행시간을 Inst Count, CPI, Clock Rate 3가지 term으로 나누어 생각해 볼 수 있음

결국 CPU 시간을 줄이려면 3가지 term 모두 줄이면 되지만 Inst Count 줄이면 각 명령어의 일의 양이 커저 cycle의 수가 증가

3개의 term에 대해 Trade off 관계를 가지고 있음

---

#### Cycles Per Instruction

CPI = (CPU Time*Clock Rate) / Instruction Count = Cycles / Instruction Count

Instruction Frequency

![Alt text](http://leesangwon0114.github.io/static/img/CA/1.16.PNG)

전체 명령어에 i번째 Intruction 개수를 통해 Frequency를 얻고 이를 각 명령어당 곱해 더하면 평균 CPI를 얻을 수 있음

Common case를 찾을 수 있음 = 명령어에 해당하는 상대적인 CPI를 얻어낼 수 있고, 시간이 많이 소모되는 것에 리소스를 투자할 수 있게됨

**Example**

![Alt text](http://leesangwon0114.github.io/static/img/CA/1.17.PNG)

---

#### SPEC(System Performance Evaluation Cooperative)

밴치마크를 위해 성능 측정을 위한 표준

각각의 머신 별 성능에 따라 평균을 구하던지 Normalized execution time을 구하던지 해서 수행시간에 대한 판단 척도로 사용


---

뒤에 부터 Basic Processor architecture : DLX/MIPS pipeline

전형적인 RISC 구조

32-bit fixed format instruction(3 formats - R, I, J)

![Alt text](http://leesangwon0114.github.io/static/img/CA/1.18.PNG)

