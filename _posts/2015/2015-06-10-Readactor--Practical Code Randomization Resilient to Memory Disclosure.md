---
layout: post
permalink: /post/readactor
tags: [ROP, Randomization, Security]
categories: [Research]
---


Readactor[^Readactor] 这篇文章发表在2015年的S&P(Oakland)上.

在讨论小组里讲了下，讲完之后浑身轻松的感觉。涉及的内容略多，此前的记录过的XnR[^XnR_Post],[^XnR_paper],Isomeron[^Isomeron_post],[^Isomeron_paper],以及Xyomoron[^Xyomoron],[^Xyomoron_summary]和Just-in-time code reuse[^JIT-code-reuse].当然理清条理后还是很好懂其中的motivation啥的。

*注*: 详细的在slides上，可以从[Slideshare](http://www.slideshare.net/ch0psticks/readactor-slides)和[百度云](http://pan.baidu.com/s/1i38aHa1)获取。

## 1. 概述

本文有点集大成于一体的感觉。涉及到的内容也较多如之前的文章
较为systematic的分析和解决了memory disclosure的问题。

1. 通过hardware-assisted的方式实现了code page的`eXecute-only`，进而解决了由代码指针在代码页中的相互引用引发的大规模内存泄漏，文中称为`direct memory disclosure`
2. 通过`code pointer hiding`实现了由于heap、stack中的函数指针、代码指针引发的代码页内存泄漏，即文中的`indirect memory disclosure`.

最终Readactor所能到达的防御效果：

1. 有效防御static ROP（需结合fine-grained code diversification）,dynamic ROP(generate ROP chain online)
2. 保护对象包括静态代码（提前编译好的代码）和动态生成的代码（如JIT-compiled code）

其它：

1. 基于Intel 的EPT，在guest physic memory到host physic memory的页表上强制实施代码页面的eXecute-only。
2. Code pointer hiding则是针对返回地址、虚表中函数指针、C++对象中的函数指针等进行保护，方法是加一个trampolines，转变为间接跳转。


## 2. 设计&实现

先欠着吧，有空来补，更多看做的一个slides (more details are presented in  slides)[^slides],[^slides2]


## 总结

其实可以看出作者对Code Reuse、memory disclosure、randomization之间的关系疏离的很清楚，然后把已有的研究方法逐一映射上去，攻击和防御的一一映射。从而找到了研究的切入点。出发点相对朴素。**但是**，每一点都做的很细致，而且拥有很强的系统研究的背景，所以才可能做出如此集大成于一体的文章。

ps: compiler-based code diversification这里似乎有点牵强啦:)，可能是为了针对static ROP吧。 把细粒度代码随机化放在编译时，实际上只能确保以不同途径distributed binary有着不同的 code layout；然而，大部分时候PC上的binary distribution时都来自同一个编译版本。如果说对于Android、iOS等，那么或许可以放在market这里。  对于PC，最好还是在loader这里实现code diversification吧，如果要是能实现**self-randomization while loading**,岂不更佳？


[^slides]:[ReadactorSlides on Slideshare](http://www.slideshare.net/ch0psticks/readactor-slides)

[^slides2]:[ReadactorSlides on 百度云](http://pan.baidu.com/s/1i38aHa1)

[^Readactor]: Crane S, Liebchen C, Homescu A, et al. Readactor: Practical code randomization resilient to memory disclosure[C]//IEEE Symposium on Security and Privacy, S&P. 2015, 15.

[^XnR_Post]: [总结《XnR：You can run but you can't read》](http://happyhacking.info/post/xnr)

[^XnR_paper]: M. Backes, T. Holz, B. Kollenda, P. Koppe, S. Nürnberger, and J. Pewny, “You Can Run but You Can’T Read: Preventing Disclosure Exploits in Executable Code,” in Proceedings of the 2014 ACM SIGSAC Conference on Computer and Communications Security, New York, NY, USA, 2014, pp. 1342–1353

[^Isomeron_post]: [总结《Isomeron: Code randomization resilient to return-oriented programming》](http://happyhacking.info/post/isomeron)

[^Isomeron_paper]: L. Davi, C. Liebchen, A.-R. Sadeghi, K. Z. Snow, and F. Monrose, “Isomeron: Code randomization resilient to (just-in-time) return-oriented programming,” in Proc. 22nd Network and Distributed Systems Security Sym.(NDSS), 2015

[^Xyomoron]: M. Backes and S. Nürnberger, “Oxymoron: Making Fine-Grained Memory Randomization Practical by Allowing Code Sharing,” in 23rd USENIX Security Symposium (USENIX Security 14), San Diego, CA, 2014, pp. 433–447. 

[^Xyomoron_summary]: 软安学孰. [论文学习(第9期)——Oxymoron(USENIX Security 2014)](http://mob.vkadoo.com/E3720F68A7E128F0B35DB03EAC523062.wxhtml)

[^JIT-code-reuse]: K. Z. Snow, F. Monrose, L. Davi, A. Dmitrienko, C. Liebchen, and A. Sadeghi, “Just-In-Time Code Reuse: On the Effectiveness of Fine-Grained Address Space Layout Randomization,” in 2013 IEEE Symposium on Security and Privacy (SP), 2013, pp. 574–588.


