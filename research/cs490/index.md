---
layout: page
title: CS 490 - R&D Project
subtitle: Microarchitecture for Datacenter Workloads
---

### Problem Description
Datacenter workloads have a very large code footprint. It means the number of instruction lines present are very large in numbers. As a result not all instruction lines can fit in L1I and even L2 cache. Thus instruction line misses are frequent during fetching of instructions which takes few hunndred to thousand cycles to fetch the line from L3 or from main memory. Many a times, decode stage stalls due to unavailability of instructions in decode queue while the instruction lines are being fetched from higher levels of cache hierarchy. This is termed as _Decode Starvation_. My aim was to optimize processors so that decode starvation is minimized.

### Papers
- [EMISSARY](https://dl.acm.org/doi/10.1145/3579371.3589097)

Video of my R&D presentation can be found [here](https://youtu.be/v48SQHWy594) <br/>
Slides can be found [here](./presentation.pdf)