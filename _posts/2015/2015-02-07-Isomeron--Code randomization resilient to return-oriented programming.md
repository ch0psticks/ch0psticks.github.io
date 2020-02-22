---
layout: post  
tags: [ROP, JIT, Randomization, Security]
categories: [Research]
permalink: /post/Isomeron
---

本文发表在2015年NDSS上[^Isomeron]。

## 1. 概述

本文的贡献包括：

1. 指出Oxymoron防御的不完备性，提出了bypass的方法；
2. 针对jit code reuse作者又提出了自己的防御scheme——Isomeron。

Isomeron实现了一个Diversifier，该diversifier能够在程序的直接和间接跳转指令的位置实现从两个具备相同semantic的跳转目标中随机选择一个的功能。其中，两个相同语义的跳转目标（Basic Block），一个是原始image中的，另外一个是fine-grained randomisation之后的image。 对于ROP攻击，使得跳转过去的目标gadget与预期的不一致——Basic block具有相同语义，但对于gadgets未必具有相同语义，ret等分支指令前的指令可能是2种情况中的一种，有不确定性，从而导致gadgets可能执行失败。  每个跳转会进行一次2选1（coin-flip)，所以对于单个gadgets准确的概率是——1/2.那么要让n个gadgets连接顺利执行，则需要0.5^n. 

安全性不高，即这种diversifier的随机性太小。

## 2. Bypass Oxymoron
Oxymoron[^Oxymoron]是针对JIT code reuse提供的一个防御方法，其将代码中的跳转指令的目标地址进行了处理，这样在JIT-ROP攻击工程中，harvest pages的操作将无法进行——无法根据跳转的目标地址获得其他页面内地址。换种方式理解——Oxymoron阻止了内存的连续泄漏。
本文中对此bypass的方法是获取堆栈上的返回地址、vtable中的函数指针地址，从而获得其他页的内存范围。进而实现havrest tpages.
下图是JIT ROP 生成payload的过程。

![Isomeron的DBI workflow](http://i.imgur.com/7fNWKmy.png?1)

## 3. Isomeron

下图给出了基于DBI的Isomeron的workflow。图中列出的是call和ret的实现，文中也说，对于jmp等直接跳转也可以用类似call的方式实现。（注：也可以用PIN或者用DynamoRIO实现BDI的目的，但是缺乏duplicate images的cache）

![Isomeron中DBI的Workflow](http://i.imgur.com/eABCmMr.png)


下图是Isomeron的Scheme.

![Scheme of Isomeron](http://i.imgur.com/fW5wMM0.png)

通过这个图，可以很清晰的看到系统框架。包括：

1. 基于Basic Block的DBI；
2. fine-grained randomisation的block instrumentation Cache和coarse randomization Instrumentation Cache的map；
3. 在instrumented instructions上，从两个具有相同Semantic的Cache中，由Diversifier随机2选一，然后执行。

## 3. 总结

1. 实际上本文方法对ROP攻击的防御，还是依赖于fine-grained Randomisation的安全性；但不同的是，文中实现的diversifier需要dynamically choose execution path at runtime,这就使得ret/jmp/call address的时候，address是从address1和address2中二选一，使得目标指令序列难以预测（address1是未经fine-grained随机化的image中的地址, address2是经过fine-grained随机化的image中的地址。）。
2. 不考虑memory disclosure的情况下，实际上Isomeron对fine-grained的randomisation的安全性是降低的；换句话说，是牺牲了随机化的程度来提供一点对抗memory disclosure的安全性。
3. fine-graind randomization本身并不能确保所有的gadgets都被随机化了；当然单个gadget中指令数阅读，gadget被随机的可能性越大，安全性就越好。
4. 本文的提出的思路，其实也是很straightforward的：pre-randomisation->runtime randomization —> dynamically random choosing —> random choose branch target
5.  high-level idea: 与Practical Control Flow Integrity and Randomization for Binary Executables[^PracticalCFI]有些相似，虽然出发点可能不尽相同。



[^Isomeron]: L. Davi, C. Liebchen, A.-R. Sadeghi, K. Z. Snow, and F. Monrose, “Isomeron: Code randomization resilient to (just-in-time) return-oriented programming,” in Proc. 22nd Network and Distributed Systems Security Sym.(NDSS), 2015.


[^Oxymoron]: M. Backes and S. Nürnberger, “Oxymoron: Making Fine-Grained Memory Randomization Practical by Allowing Code Sharing,” in 23rd USENIX Security Symposium (USENIX Security 14), San Diego, CA, 2014, pp. 433–447.

[^PracticalCFI]: C. Zhang, T. Wei, Z. Chen, L. Duan, L. Szekeres, S. McCamant, D. Song, and W. Zou, “Practical Control Flow Integrity and Randomization for Binary Executables” in 2013 IEEE Symposium on Security and Privacy (SP), 2013, pp. 559–573.


