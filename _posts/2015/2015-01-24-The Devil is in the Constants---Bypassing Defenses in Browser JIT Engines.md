--- 
layout: post  
categories: [Research]
tags: [ROP, JIT, Security]
permalink: /post/the-devil-is-in-the-constants-bypassing-defenses-in-browser-jit-engines
---


这篇文章发表在NDSS 2015上。下面写写关于此文的一点总结。

### 总结

该文主要展示了：*可以不在已加载到内存中模块的代码中寻找gadgets就能实施ROP攻击*。

具体地，通过在JavaScript中巧妙的定义常量，使得这些数据被JIT compiler编译为native code后恰好转变为所需的rop gadgets，从而为攻击者所用。

这种基于JIT的代码动态生成技术，配合信息泄漏漏洞能够bypass现有防御技术：

- 由于gadgets源于非已加载模块，因此可以不受fine-grained 随机化的影响；关于位置定位则可以结合`Just in time code reuse`[^JustInTime]的
- 此外，对于XnR[^XnR]技术也很难防止这类攻击，因为代码是动态生成，且不存在于代码段，或许通过修改方案使其也保护堆空间，则有可能防止。


文中还分别针对Firefox和IE9这两个浏览器不同JIT Engine的情况进行了分析。其中IE9没有开源，且其JIT Engine——`Chakra`具有一定针对JS的防护机制。具有较高的难度。

[^XnR]: [You Can Run but You Can't Read: Preventing Disclosure Exploits in Executable Code](http://dl.acm.org/citation.cfm?id=2660378)

[^JustInTime]: [Just-In-Time Code Reuse: On the Effectiveness of Fine-Grained Address Space Layout Randomization](http://ieeexplore.ieee.org/xpls/abs_all.jsp?arnumber=6547134&tag=1)


### 反思

#### 1.JIT 本身

这种利用JIT代码动态生成技术可以动态的生成所需的gadgets。本文使用js 中的constant生成所需的gadgets指令块，这些constant本身属于数据，那么通过jit compiler编译后，变为了native code——问题来了：

- JIT为啥没把数据和代码分开？如果分开了，那么很明显这些构造和编译后的常量，仍然无法被执行。
- 有没有可能既是JS代码，恰好编译后也是gadgets？这相对常量更加难以实现，但更具攻击力

#### 2.Android等其他支持JIT的平台
Android也支持JIT，那么有没有可能在`Java->Dex—-[JIT]—>native code`，这一链条中poison java部分的内存，从而可以更加从容的生成所需的gadgets，以供native shared library中的native code调用和使用。


---

此外，与本文相关的还有同发表在NDSS 2015上的《[Exploiting and Protecting Dynamic Code Generation](http://chao.100871.net/papers/NDSS2015-DCG-CR.pdf)》.


