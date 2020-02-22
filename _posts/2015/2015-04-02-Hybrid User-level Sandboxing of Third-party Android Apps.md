---
layout: post
Title: Hybrid User-level Sandboxing of Third-party Android Apps
permalink: /post/AppCage
categories: [Research]
tags: [Sandbox, Android, Security]
---


本文是Yajin牛发表在AsiaCCS‘15的，可以简称为AppCage[^appcage]，有幸拜读。遗憾的是提前离开坡县了，木有机会参会了。欣慰的是离开前参加了Yajin的一个talk :).

看完有点久了，随便写写:)，如有错误、不当，欢迎看官指出，见谅

## 1. Introduction

从标题可以看出这篇文章是做AppSandbox的，之所以称为Hybrid，因为作者对应用程序的Dalvik Bytecode和native code分别做了sandbox。

- 对于应用程序的bytecode的sandbox，则是通过注入Framework，拦截一些关键API。
- 对于Native code的sandbox，则是利用SFI（software fault isolation）[^SFI]的概念、结合Binary rewriting的方法实现的。

**advantages**：

- 在现有的系统中很容易集成进去；
- 能够有效限制与DVM运行在同一安全边界上的native code的攻击；
- 能有效避免由native code的memory corruption导致应用程序被compromised的情形;
- 对Java层的访问控制则不言而喻了。

**side effect**：

- BinaryRewriting改变了程序（so）的完整性，作者使用了自签名的方式进行再次签名。
- 更新，通过其他渠道进行更新:(。对ecosystem破坏略大。

怎么说呢，说起来，都还比较straightforward，但实现起来却是苦差吧，应用上嘛，却是如作者所说，在现有的情况下，这样做也能提升一定的安全性。各取所需了。

## 2. Implementation

看图不说话吧。

Architecture of the **AppCage**:

![Appcage Architecture](http://i.imgur.com/au9RPiv.png)


Memory layout of the native sandbox(SFI)

![memory layout of native sandbox](http://i.imgur.com/ovlT1b7.png)

Dalvik Virtual Machine Instrumentation

![Dalvik-Hook](http://i.imgur.com/DPMpzoj.png)

## 3. Discussion

### 3.1 Binary Rewriting. 

碰巧博主也做这个，也是对ARM做的。基于Binary rewriting 实现SFI，那么需要对native code做很大修改的，不仅是in-place的code transformation，还需要插入新的功能代码。本人仅仅做的是in-place的修改，就已经够折腾了，需要cover各种情形，不容易。yajin兄更是可想而知了。 

大致的挑战性问题有：

1. identify data embedded in code
2. the mixture of various types of instructions:ARM, 32-bit Thumb, 16-bit Thumb.
3. 由compiler optimization引发的各种奇葩cases，导致函数识别不完全、部分basic block链接不进去。
4. 没有固定的frame pointer。arm中可能会用`r11`作为function的frame pointer，但是对于Thumb则似乎木有特别固定的。
5. 完整的CFG。这个可以说太重要了。否则多返回、各种奇葩的返回等，都要依赖于CFG来识别，然后调整栈帧平衡。
6. ... 

最后说一句，Binary Rewriting，，谁做谁知道。当然也许别人有高招吧:)

### 3.2 NativeCode Security and SFI

文中是用了SFI[^SFI]，个人简单理解为对内存访问进行隔离。这个概念其实提出也挺早了。但似乎近几年一直在断断续续的推进。 尤其是对于JVM中native code的isolation上，用的更多些。

- Google Chrome用的NativeClient（NaCl）[^NaCl]
- Yajin兄的ARMLock
- 针对JVM中native code的防护：Lehigh University的Gang Tan的group的研究[^jvmsandbox] and [^javanativecode]。

*(注：上面只是随便列举了，并不完全，或者不一定是关键文献)*


### 3.3 Framework API Hook

这一层的sandbox就没啥多说的了。看上面的图就知道了。



[^appcage]: Yajin Zhou, Kunal Patel, Lei Wu, Zhi Wang, and Xuxian Jiang. “Hybrid User-Level Sandboxing of Third-Party Android Apps.” In Proceedings of the 10th ACM Symposium on Information, Computer and Communications Security. Singapore, 2015.

[^NaCl]: Yee, B., D. Sehr, G. Dardyk, J.B. Chen, R. Muth, T. Ormandy, S. Okasaka, N. Narula, and N. Fullagar. “Native Client: A Sandbox for Portable, Untrusted x86 Native Code.” In 2009 30th IEEE Symposium on Security and Privacy, 79–93, 2009. doi:10.1109/SP.2009.25.

[^jvmsandbox]: Sun, Mengtao, and Gang Tan. “JVM-Portable Sandboxing of Java’s Native Libraries.” In Computer Security – ESORICS 2012, edited by Sara Foresti, Moti Yung, and Fabio Martinelli, 842–58. Lecture Notes in Computer Science 7459. Springer Berlin Heidelberg, 2012. http://link.springer.com/chapter/10.1007/978-3-642-33167-1_48.

[^javanativecode]: Sun, Mengtao, Gang Tan, Joseph Siefers, Bin Zeng, and Greg Morrisett. “Bringing Java’s Wild Native World Under Control.” ACM Trans. Inf. Syst. Secur. 16, no. 3 (December 2013): 9:1–9:28. doi:10.1145/2535505.

[^SFI]: Wahbe, Robert, Steven Lucco, Thomas E. Anderson, and Susan L. Graham. “Efficient Software-Based Fault Isolation.” In Proceedings of the Fourteenth ACM Symposium on Operating Systems Principles, 203–16. SOSP ’93. New York, NY, USA: ACM, 1993. doi:10.1145/168619.168635.


