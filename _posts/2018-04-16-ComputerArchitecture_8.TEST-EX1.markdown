---
layout: post
title:  "Computer Architecture&nbsp;8.&nbsp;TEST-EX1"
date:   2018-04-16 03:28:15 +0700
categories: [ComputerArchitecture]
---

#### [1] 3가지 형태의 Cache miss를 각각 정의하고 발생하는 이유를 설명하시오. 

compulsory miss - 캐쉬가 처음 아무것도 안채워진 상태에서 발생하는 miss로 cold start miss라고도 함

capacity miss - 캐쉬가 모두 차서 공간이 부족해 발생하는 miss

conflict miss - Associate로 인해 특정 index의 제한된 공간에서 발생하는 miss

---

#### [2] 어느 가상 시스템에서 수행되는 응용 프로그램의 명령어 구성 분포는, ALU 명령어가 1 cycle이 소요되며 40%를 차지하고, LOAD 명령어가 2 cycles가 소요되며 25%, STORE 명령 어가 2 cycles가 소요되며 20%, BRANCH 명령어가 2 cycles가 소요되며 15%를 차지한다고 한다. 이 때, 이 프로그램 수행시의 CPI (average cycles per instruction)를 구하시오. 

![Alt text](http://leesangwon0114.github.io/static/img/CA/8.2.PNG)

0.4 + 0.5 + 0.4 + 0.3 = **1.6 CPI**

---

#### [3] CPU의 pipeline 구조에서 정해진 cycle time에 다음 명령어를 진행시키지 못하는 경우 Hazard가 발생하여 Pipeline 성능이 감소하게 되는데, 3개의 Hazard증 어느 것이 CPI에 가장 큰 영향을 미치며, 그 이유를 프로그램 수행 특성을 이용하여 설명하시오. 

Structural hazards, Data hazards, Control hazards 중 Control hazards가 CPI에 가장 큰 영향을 미친다.

프로그램은 locality 로 인해 특정 시점의 address space의 일부분에서 계속 움직이는 특성으로 다시 수행되어 hit rate가 높다. 그러나 branch가 일어나게 되면 캐쉬에 다른 address space가 채워져야하므로 miss rate가 일시적으로 증가하여 CPI에 가장 큰 영향을 미친다.

---

#### [4] 직접사상 캐쉬 (Direct-mapping cache)의 참조 성능과 2-way set associative cache의 cache miss를 지원하기 위하여 이중 구조의 Main cache와 Shadow cache를 설계하여 한다. 1) 구성도, 2) 각 버퍼내의 히트 (hit)와 미스 (miss) 상황을 반영하는 전체 개념적 동작 Flow.

![Alt text](http://leesangwon0114.github.io/static/img/CA/8.1.PNG)

---

#### [5] Write back 구조의 Cache에서 read miss (cache가 full 상태)시 이를 처리하는 과정을 기술하고 성능적 측면에서 이 과정을 분석해 보고 개선 방안을 제시하시오

read miss가 dirty block을 replacing 해야하는 상황 발생

일반적으로 read miss 시 write back에서는 아직 메모리에 안쓰였으니까, 업데이트 되어 dirty flag가 1이므로 메모리에 write해준 후 read 해야함.

이 때 read만 하려고 했는데 write까지 해야하는 성능적인 이슈가 존재

이를 개선하기위해 Read에 우선순위를 두는 방법이 존재

-> 먼저 write buffer에 dirty block을 copy 한후 read를 해준 후 write buffer가 write
