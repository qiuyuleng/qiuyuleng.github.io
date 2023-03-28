---
title: "Database"
typora-root-url: ../
date: 2023-03-28T16:05:32+08:00
draft: false
---


AR: Why Intel's driver is open-source, and Nvidia's driver is not open-source. How about AMD?

1. AMD也是开源的

2. Intel和AMD开源是因为CPU指令集就那几种，大家都是统一的

   > 什么是CPU指令集：
   >
   > 1. reduced instruction set computing 精简指令集，ARM
   > 2. complex ... 复杂指令集，Intel

3. GPU的指令系统更复杂，有发送给不同单元的控制指令，还有发给流处理器并行执行的流处理器指令，暴露了GPU的指令页面在一定程度上就暴露了GPU的硬件设计（Open-sourcing their drivers would reveal a lot of how their hardware worked, and give away secrets to their competitors.）

4. From Nvidia: Most customers require Nvidia to provide an e2e solution, which means Nvidia wholly responsible for product delivery and support, including the drivers. Therefore, Nvidia decide to retain source code control. (2021.9)

5. BTW, 苹果也不开源


如何Ramp up？

https://zhuanlan.zhihu.com/p/595987425

1. 《数据库系统概念》《Database System Concepts》
2. 《数据库系统实现》《Database System Implementation》
3. CMU 15-445，教材 《Database System Concepts》
   1. https://15445.courses.cs.cmu.edu/fall2019/schedule.html
4. CMU 15-721 （进阶）
   1. https://15721.courses.cs.cmu.edu/spring2020/schedule.html