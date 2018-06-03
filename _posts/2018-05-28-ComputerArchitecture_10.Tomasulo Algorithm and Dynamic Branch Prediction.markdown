---
layout: post
title:  "Computer Architecture&nbsp;10.&nbsp;Tomasulo Algorithm and Dynamic Branch Prediction"
date:   2018-06-03 03:28:15 +0700
categories: [ComputerArchitecture]
---

#### Tomasulo Alogorithm vs Scoreboard

dynamic algorithm의 또 다른 방법인 Tomasulo(High Performance without special compiler)

Control & Buffers 가 분산되어 있음 vs 집중화됨

- FU Buffers 는 reservation stations이라고 부름

Function Units과 reservatino stations은 Common Data Bus로 연결되어 있음(Broadcast)

Load, Store 도 RS로 간주해서 생각

---

#### Tomasulo Organization

![Alt text](http://leesangwon0114.github.io/static/img/CA/10.1.PNG)

---

#### Reservation Station Components

**Reservation station:**

- op: unit을 수행할 operation
- Vj, Vk: Source operands의 값
- Qj, Qk: source registers를 생산하는 Reservation station
- Busy: FU is busy

**Register result status:** 어떤 functional unit이 각각의 register 에 write할 것인지를 가르킴

---

#### Three Stages of Tomasulo Algorithm

1. Issue: FP Op Queue에서 instruction 을 가져옴

	reservation station이 free 하여 structural hazard가 없을 경우 issues 하고 operands를 보냄

2. Execution: operands를 가지고 연산 실행
	
	operands 가 두 개다 ready 되면 execute

	만일 ready 되어 있지 않다면 Common Data Bus에서 결과를 기다림

3. Write result: 실행 완료
	
	Common Data Bus에 Write하여 기다리는 units에게 전달


Normal data bus : 특정 destination으로 가는 것(go to bus)

Common data bus : data + source i.d(come from bus)

---

#### Tomasulo vs Scoreboard

Piplelined Functional Units vs Multiple Functional Units

windows size: 14 instructions vs 5 instructions

No issue on structural hazard same

WAR: renaming avoids vs stall completion

WAW: renaming avoids vs stall completion

Broadcast results from FU vs Write/read registers

Control: reservation stations vs central scoreboard

---

#### Tomasulo Drawbacks

- 복잡함
- 여러개가 동시에 stores 시 high speed CDB 필요
- 여러개의 Common Data Bus를 쓰면 더 많은 Funtional Unit과 동시에 처리가능하지만 cost 와 복잡도가 증가함

---

#### Tomasulo Summary

Reservations statins 을 통해 dynamic renaming 을 수행(WAR, WAW 해저드 제거, HW적으로 loop unrolling 수행하는 것 처럼 보임)

basic blocks에 제한되어 있지 않음

Dynamic Scheduling, Register renaming, Load/Store disambiguation 에 기여함

Register bottleneck 제거
