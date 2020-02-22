---
layout: post
Tags: [Detours, Hook]
categories: [Research]
permalink: /post/trampoline-in-detours.html
---

本文主要是翻出来的以前自己在看Detours实现时对Trampoline的一些理解。

## 1. 关于Detours

Detours是一个可以用来拦截ARM、x86、x64和IA64平台上任意win32二进制文件中函数的库，拦截代码能够在程序运行时动态的应用。

目前已经更新到3.0版本。分为Professional和Express版本，一般用途的话，免费的Express版本足以应付。此外，Express仅支持X86机器上的32位进程，而Professional版本可以支持X86机器上的32位和64位进程。关于具体细节,可以关注Microsoft Research的 [项目主页](http://research.microsoft.com/en-us/projects/detours).


## 2. Detours的基本原理

### Detours的基本思路

1. 用可以无条件跳转到用户提供的detour函数的指令替换了原来目标函数中前几字节的指令；
2. 而目标函数中的前几条指令被放置到一个trampoline中，Trampoline的地址被保存在一个指针中；
3. Detour函数的功能既可以完全替换目标函数，也可以在其中通过调用指向trampoline的目标指针，从而把修改后的目标函数作为一个子函数（例程）来调用，**从而在实现原有功能的基础上，扩展其语义**。

下面一副图的上图显示了在不拦截的情况下的函数调用，下图则显示了利用Detours实现拦截后的代码块的跳转/执行流程。

![DetoursBasic](http://i.imgur.com/vYTt2.jpg 'Control flow of detours')

下图这幅图则显示了拦截前后，TargetFunction和Trampoline Bloc的代码。

![DetoursCodeBlocks](http://i.imgur.com/HmkSZ.jpg 'detour前后的代码变化')



## 3. Trampoline in Detours


下面这是我所理解的Detour流程:

![ControlFlow](http://i.imgur.com/XGcH5.jpg '我所理解的跳转流程和内存布局')

其中共有4个代码块，之所以这么划分只是因为这4个code block在内存中不是连续的：

* 第一个是正常函数的code block
* 第二个是被hook函数的所在的code block
* 第三个是自己申请的一块内存，用来保存目标函数的前五个字节及其一条跳转指令
* 最后一块是自己写的函数的code block

整个执行流程共6步，分别如下：

1. 原始程序按照正常流程调用目标函数`TargetFunction`；
2. 由于`TargetFunction`前5个字节根据inline hook需要，已被替换为跳转指令，即`jmp DetourFunction`,故这步表示为`TargetFunction`被hook，流程被劫持到预定义的插入的自定义的额外代码块或函数上`DetourFunction`上；
3. 在`DetourFunction`中，可能需要实现原始功能，故需要调用真正的目标函数，即通过调用`Trampoline Block`作为跳板跳转到`TargetFunction+5`处。故3为跳转到`Trampoline block`
4. 跳转到真正的目标函数代码块上
5. 执行完真正的目标函数功能后返回自己的函数，继续其他操作
6. 瞒天过海，一切结束后，返回到源函数

## 4. 题外话

在最早看detours之前，其实自己已经写过类似的inline hook的代码，但其通用性和稳定性终究无法和detour相提并论。从会做一件事儿到做到极致，是完全不同的境界；可是很多时候我们需要什么来支撑我们做到极致呢？

