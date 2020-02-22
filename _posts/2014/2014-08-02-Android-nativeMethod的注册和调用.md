---
layout: post
tags: [Android, JNI, Mobile, Security]
categories: [Research]
permalink: /post/native-method-registeration
---

在Android中，可以通过JNI的方式来调用和访问用C/C++实现的代码，这些代码以SharedLibrary的方式存在于so中。从Java Code到Native Code的一般使用过程为：

1. 在Java中的某个类中调用`System.loadLibrary(XXX)`*(对于ing的so的全名应为：libXXX.so)*，或者`System.load(soPathName)`*(soPathName对应于要加载的某个so的完整路径及文件名)*来加载so；
2. 声明所要调用的静态方法，即需要和so中函数关联的方法名：需要关键字`native`。
3. 编写so，实现声明的native method的功能。
4. 与nativeMethod进行关联：1）使用符合JNI规范的JNI Name String来作为so中的对应函数的函数名：一般为class_name_method_name的形式；2）在JNI_OnLoad中调用jniRegisterNativeMethods手动注册，一半对于每个method需要提供信息:methodName, method signature, function(pointer). 

本文则是总结native Method与so中的function如何建立关联关系的：jni native method的注册。对于完整注册过程可参考老罗的博文[Dalvik虚拟机JNI方法的注册过程分析](http://m.blog.csdn.net/blog/luoshengyang/8923483)，其中已有详尽的介绍。


### 通过jniRegisterNativeMethods的注册

这个方式的注册，是用户在JNI_OnLoad中主动注册完成的。重新学习注册过程，了解了method的管理方法和调用方式。
注册过程就是一个将native function绑定到一个native method，准确点说应该是将该native method结构中的`DalvikBidgeFunc nativeFunc`赋值为某一个jniBridge函数，然后将ins赋值为native function的函数地址。然后再调用时通过调用`nativeFunc`，即bridge函数，在此基础上基于libffi，实现跨平台无差异化调用真正的对应的native function。
关于java 中method的结构可参考对Java中method和class的管理和组织方法。


在注册过程中会，会根据注册提供的信息－－className、MethodName去反射获取对象，查看需要绑定的对象和native method是否存在。


### 符合jni specification的本地函数的查找过程

*相关代码位于：dalvik/vm/native.cpp*

由于此种方式的调用，java中的native method中的nativeFunc被初始化的设置为dvmResolveNativeMethod；(实际上每个method都被初始化为dvmResolveNativeMethod)。

所以首次运行时会调用`dvmResolveNativeMethod`尝试去内部（ dvmLookupInternalNativeMethod(method)）以及已加载的so中尝试获取该相应的函数地址（ lookupSharedLibMethod(method)， 实际搜索过程在findMethodInLib中完成）.如果是在lib中找到， 则通过dvmUseJNIBridge将method中的nativeFunc设置为相应的Bridge函数，ins设为func地址。

此后则无需搜索过程，直接调用即可。

对于在so中的函数搜索，判断依据是lib和method有相同的classLoader，且method对应的JNI name string的函数存在于lib中.（这通过dlopen和dlsym尝试获取地址来判断）。

### 参考
1. 老罗 [Dalvik虚拟机JNI方法的注册过程分析](http://m.blog.csdn.net/blog/luoshengyang/8923483)
2. AOSP source code

