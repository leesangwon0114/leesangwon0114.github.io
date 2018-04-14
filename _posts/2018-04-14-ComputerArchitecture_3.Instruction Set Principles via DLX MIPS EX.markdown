---
layout: post
title:  "Computer Architecture&nbsp;3.&nbsp;Instruction Set Principles via DLX MIPS EX"
date:   2018-04-14 02:28:15 +0700
categories: [ComputerArchitecture]
---

Instruction Set Architecture 정의하는 것이 목표

Simple하지만 효과적인 명령어 셋을 이용하기 위함임

-> 이를 통해 Pipeline Structure를 디자인함

---

#### System Applications

시스템 응용군은 크게 3가지로 나뉨

Desktop - INT & FP 동작이 매우 중요하지만 파워, code 사이즈 신경 쓰지 않음

Server - FP 연산이 중요하지 않음

Embedded - cost, power, code 적을 수록 좋음

**응용군에 따라 명령어들을 비슷하게 쓰는 결론을 얻음**

---

#### Classifying Instr Set Architectures

**Accumulator** - 1 register

**Stack**

**Reg-Mem**(general purpose register, M-M)

- 2 or 3 address 

**REG- REG**(Load / Store)

##### General Purpose registers 사용하는 이유?

- memory 보다 빠름

- 컴파일러 입장에서도 변수 등에 관리 용이(speedup, code density)

![Alt text](http://leesangwon0114.github.io/static/img/CA/3.1.PNG)

R-R 타입은 Risc로 간단하게 code 생성가능하지만 Instruction 개수가 엄청 많아

DLX 는 code uniformity를 위해 R-R 을 선택

---

#### Memory Addressing

**Addressing mode** - memory가 있을 때 명령어 에서 어떤 데이터를 access 하고자하는 메모리의 주소를 만들어내야하는데 이를 **effective address**라하고 이 address를 만들어내는 함수를 addressing mode - 내가 원하는 데이터의 메모리 위치를 얻는 것

---

#### Addressing Modes

어떤 Addressing Mode인가에 따라 IC(총명령어의 개수)에 영향을 미침

특정기준머신 VAX를 선정해 3개 프로그램마구 돌려 측정함

VAX는 구닥다리 기계인데 모든 가능한 Address Mode 다 가지고 잇음

Displacement가 평균적으로 42% 활용하는 것으로 파악됨

두가지(Displacement, Immediate)만 사용해도 75% 는 커버됨

Common case를 support 하는 설계 원칙 적용

별로 쓰지 않는 것은 지원 안함

88퍼 이상 커버하는 것만 선택하겠다~

(Displacement, Immediate, Register)

![Alt text](http://leesangwon0114.github.io/static/img/CA/3.2.PNG)

--- 

#### Immediate addressing

Immediate addressing은 arithmetic & comparison에 많이 사용됨

명령어 포맷을 보면 Immediate range 결정해 주어야함

![Alt text](http://leesangwon0114.github.io/static/img/CA/3.3.PNG)

Immediate data를 16bit 만 있으면 80% 정도 커버 가능하다

![Alt text](http://leesangwon0114.github.io/static/img/CA/3.4.PNG)

---

#### Displacement Address Size

기준값에서 얼마만큼 떨어진 offset 값을 표현하는 곳의 범위 확인

![Alt text](http://leesangwon0114.github.io/static/img/CA/3.5.PNG)

12 ~ 16bit면 75~99% 까지 cover 가능

---

#### 현재까지 Memory Addressing 요약

![Alt text](http://leesangwon0114.github.io/static/img/CA/3.6.PNG)

---

#### Instruction Types

어떤 명령어들이 활용되는지 조사

![Alt text](http://leesangwon0114.github.io/static/img/CA/3.7.PNG)

복잡한 명령어 다 필요 없고 Simple 한 것 10개 지원하면 96% 프로그램 지원 가능(Common Case로 실용 성능이 높아짐)

---

#### Control Flow Instructions

PC-relative : 현재에서 상대적인 +, -


점프해야하는 range는 어떻게 결정할까?

![Alt text](http://leesangwon0114.github.io/static/img/CA/3.8.PNG)

8bit 정도 되면 90% 커버 가능

(-128 ~ +127 range에서 대부분은 다 커버한다는 뜻)

conditional 중 EQ/NE가 86%로 대부분임(LT/GE/GT/LE 보다)

- simpe한 == 비교를 사용

---

#### Encoding Instruction Set

Encoding - 
32bit 명령어에 operand 등을 어떻게 내재 시킬 지

code size 가 중요하면 instruction size를 변형시켜 사용하고 performance가 중요하면 fixed 된 instruction 길이를 사용함

하나의 address에 하나의 operand 배정해 사용

---

#### MIPS(DLX) Architecture

위의 분석들을 통해 설계된 Instruction set architecture

![Alt text](http://leesangwon0114.github.io/static/img/CA/3.9.PNG)

