---
layout: post  
tags: [Android, ROP, ARM, Security]
categories: [Research]
permalink: /post/ROP-without-return-on-arm
---

这篇文章实际上是一个[Report](http://www.trust.informatik.tu-darmstadt.de/fileadmin/user_upload/Group_TRUST/PubsPDF/ROP-without-Returns-on-ARM.pdf)，基本信息如下：
	
	Davi, Lucas, et al. "Return-oriented programming without returns on ARM." System Security Lab-Ruhr University Bochum, Tech. Rep (2010).

与这篇文章相关的还有一篇稍早发在CCS'10上的[文章](http://dl.acm.org/citation.cfm?id=1866370)：

	Checkoway, Stephen, et al. "Return-oriented programming without returns." Proceedings of the 17th ACM conference on Computer and communications security. ACM, 2010.
	
两篇文章都有作者Daiv Lucas, 说明其在ROP相关的研究的深刻。（注：在US-Blakhat 2013上也做了Just-In-Time Reuse的报告，与此相关的还有其发在S&P'13上的文章同一主题文章[Just-In-Time Code Reuse: On the Effectiveness of Fine-Grained Address Space Layout Randomization](http://dl.acm.org/citation.cfm?id=2497621.2498135&coll=DL&dl=GUIDE&CFID=295411095&CFTOKEN=53218921)

---

言归正传，就ROP without reuturn on ARM此文而言，则介绍了ARM上指令的特定，论证并实现了利用`BLX`、`BX`等指令作为跳转的ROP。在论证的过程中，则首先证明了这种实现方式的图灵完备性Turing-completeness，即能否用gadget构造并模拟图灵机的各种操作，如跳转、内存读写访问、计算等。最后在Android上做了ROP的demo程序，实现了启动其他程序的功能。在Demo程序中则是通过覆盖存储寄存器状态的变量实现获取程序控制权的，具体代码如下。

	struct foo{
		char buffer [200];
		jmp buf jb;
	};
	
	jint Java com example hellojni HelloJni doMapFile(JNIEnv⇤ env, jobject thiz)
	{
		// A binary file is opened (not depicted)
		...
		struct foo	f = malloc(sizeof f);
		i = setjmp(f->jb);
		if (i!=0) return 0;
		fgets (f->buffer, sb.st size, sFile);
		longjmp (f->jb ,2);
	}

其中setjmp和longjmp是两个一般不多见的c函数。大致就是setjmp保存当前栈帧、环境上下文，然后在longjmp调用时，实施状态恢复，从而实现跳转。一段摘录的更详细的介绍如下：

>setjmp和longjmp配合使用来进行程序异常错误处理，学校学习阶段基本不会用到。

>1. setjmp(j)设置“jump”点，用**正确的程序上下文填充jmp_buf对象j**。这个上下文包括程序存放位置、栈和框架指针，其它重要的寄存器和内存数据。当初始化完jump的上下文，setjmp()返回0值。 　
2. 以后调用longjmp(j,r)的**效果就是一个非局部的goto或“长跳转”到由j描述的上下文处（也就是到那原来设置j的setjmp()处）**。当作为长跳转的目标而被调用时，setjmp()返回r或1（如果r设为0的话）。（记住，setjmp()不能在这种情况时返回0。）

因此很容易看出fgets覆盖了jmp buf对象的内存，从而可以篡改决定执行流程的重要寄存器的值。

作者的payload就是通过system命令启动一个程序，shell中启动apk的方式：

	am start a android.intent.action.MAIN c android. intent.category.TEST n com.android.term/.Term

当然，`am start`其他参数亦可启动apk。如

	am start -n com.tencent.mobileqq/com.tencent.mobileqq.activity.SplashActivity
	
获取mainactivity则可以通过aapt命令:

	aapt dump badging ~/Downloads/qq_4.6.1.2110_android.apk

总的来说，这篇文章对于Android上的ROP研究，具有一定的启发意义。

