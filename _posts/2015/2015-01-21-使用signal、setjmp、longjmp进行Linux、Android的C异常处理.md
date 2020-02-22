--- 
layout: post  
tags: [Linux, Android]
categories: [Research]
permalink: /post/exception-handling-via-signal-setjmp
---

在Linux编程时，经常会看到segment fault等程序crash之后的提示，其实这是程序没有接管相应的异常处理，最后由系统进行处理的结果。那么如果程序自身想在发生异常的时候进行异常处理该怎么办呢？

在Linux，以及底层基于*nix的系统，如Android中，通常可以使用signal、setjmp和longjmp相互配合进行异常处理。


## 函数及变量说明

- **bsd_signal**： 设置异常signal的回调处理函数。
- **setjmp**: 用来保存当前的运行上下文。
- **longjmp**： 用来跳转到保存的上下文上去执行。
- **jmp_buf**：定义的上下文缓冲区。保存的程序某一时刻的运行上下文，其中包括寄存器值，以及其它一些值（具体参考jmp_buf的结构定义。）。在longjmp的时候还原执行上下文。

此外，longjmp和setjmp直接在需要的时候可以用来做跳转。并非一定要在异常时还原上下文。

## 例子


一个缓冲区溢出函数，导致segment fault 出发异常后的捕获和处理。

```C
#include <stdio.h>
#include <stdlib.h>
#include <signal.h>
#include <setjmp.h>
jmp_buf sigsegv_buf;


void sigsegv_callback(int sig_num){
    printf("recieve a segment fault signal! [%d]\n", sig_num);
    longjmp(sigsegv_buf, 1);
    return;
}
void vulfunc(){
    char dest[4];
    printf("into vulfunc\n");
    strcpy(dest, "deadbeefdeadbeefdeadbeefdeadbeef");
    printf("%s\n", dest);
    return;
}

int main(){
    bsd_signal(SIGSEGV, sigsegv_callback);
    int res;
    res = setjmp(sigsegv_buf);
    if(res==0){
        vulfunc();
    }else if(res==1){
        printf("recovered from segment fault!\n");
    }
    printf("end\n");
    if(res!=0){
        exit(0);
    }
    return 0;
}
```


main函数中利用signal／bsd_signal函数注册异常处理函数 sigsegv_callback.

1. 用setjmp设置程序恢复点（保存寄存器状态）
2. 调用vulfunc，发生溢出，导致segment fault
3. 执行到sigsegv_callback
4. 在sigsegv_callback中使用longjmp从保存的状态中恢复程序到setjmp的下一条指令，设置setjmp的返回值为非0
5. setjmp返回的是longjmp中设置的非0值，从而跳转setjmp后面的另一分支。


在Android（native executable file和Java＋jni ）测试。

亦可参考：http://www.csl.mtu.edu/cs4411.ck/www/NOTES/non-local-goto/sig-1.html

*NOTE：在有漏洞程序中，可以考虑用自己控制的数据、代码指针等构成的jmp_buf覆盖已有jmp_buf,然后再想办法使之调用logjmp，实现执行攻击代码*

