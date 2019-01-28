### Implementation of Protothread and C Coroutine

本文讲解Protothread，达夫设备和C Coroutine的实现。

**Protothread**

讲解并发Concepts的时候，我们提到了Protothread实现的关键点。这些关键点如下：

- Protothread就是一个C函数。该函数：
  - 不使用局部变量（无栈）
  - 通过全局变量记录函数重入之后开始执行的位置。该位置信息通过C的\_\_LINE\_\_宏实现。
- 非阻塞系统调用如果返回错误，则函数退出，实现运行权限的让渡。函数从下次从记录位置运行的时候，再次调用系统调用。

这里我们将结合一段代码，来具体讲解。代码来自[Ref1](#1)。

首先， Protothread库定义了一组宏（为了说明问题，这组定义是简化过的。）：

```c
struct pt { unsigned short lc; };
#define PT_WATING 0
#define PT_EXITED 1
#define PT_ENDED 2
#define PT_THREAD(name_args)  char name_args
#define PT_BEGIN(pt)          switch(pt->lc) { case 0:
#define PT_WAIT_UNTIL(pt, c)  pt->lc = __LINE__; case __LINE__: \
                              if(!(c)) return 0
#define PT_END(pt)            } pt->lc = 0; return 2
#define PT_INIT(pt)           pt->lc = 0
```

这组定义非常简单。唯一有点奇怪的是，可能是有几个宏展开之后似乎会有语法错误。嗯，不着急，继续看。

接着，[Ref1](#1)给出了一个完整的使用protothread宏的例子：

```c
#include "pt.h"
 
static int counter;
static struct pt example_pt; // pt 结构体 example_pt
 
static
PT_THREAD(example(struct pt *pt))  // 基于 protothread 实现的 example 函数
{
  PT_BEGIN(pt);
  
  while(1) {
    PT_WAIT_UNTIL(pt, counter == 1000);
    printf("Threshold reached\n");
    counter = 0;
  }
  
  PT_END(pt);
}
 
int main(void)
{
  counter = 0;
  PT_INIT((&example_pt));  // 等价于 (&example_pt)->lc = 0
  
  while(1) {
    example(&example_pt);  // 调用 example 函数，参数为 example_pt 地址
    counter++;
  }
  return 0;
}
```

example函数，在预处理阶段会展开成如下函数：

```c
static
char example(struct pt *pt)
{
  switch(pt->lc) { case 0:
 
  while(1) {
    pt->lc = 12; case 12:
    if(!(counter == 1000)) return 0;
    printf("Threshold reached\n");
    counter = 0;
  }
 
  } pt->lc = 0; return 2;
}
```

仔细观察展开的example函数，里面包含了protothread实现的全部魔法：

* PT_THREAD宏仅仅在函数名称前面增加了char。
* PT_BEGIN和PT_END闭合了一个C switch语句。
* PT_INIT初始化pt->lc为0。即为PT_WAITING状态。
* PT\_WAIT\_UNTIL利用C语言的\_\_LINE\_\_宏，将pt->lc赋值为宏所在的行数。观察原函数中，PT\_WAIT\_UNTIL所在行数为12，因此展开后，pt->lc的值为12。这一点非常重要。

下面来看函数的执行流程：

* 首先，main函数利用PT_INIT，将exampt_pt的lc变量初始化为0。
* 第一次调用example函数之后，保证case 0条件触发的语句得以执行。
* 赋值exampel_pt->lc为12。随后case 12条件满足，监测 counter == 1000 是否成立，如果不成立，return 0。
* main函数的while循环将counter自增1。
* 再次调用example函数，此时，exampel_pt->lc值为12，case 12条件满足，监测 counter == 1000 是否成立，如果不成立，return 0。
* 上述2步反复执行，直到counnter==1000成立。成立之后，打印调试信息，赋值counter为0，再次循环。

观察一下以上实现的几个关键点：

* example函数没有局部变量。

* 通过使用\_\_LINE\_\_宏，记录函数重入的位置。由于代码行数是单调递增的，即使多次使用PT_WAIT_UNTIL宏，也不会相互混淆。

* 在此例中，检测条件counter==1000的作用，大致相当于非阻塞I/O检测。可见，此处的系统调用需要是非阻塞的。

* 观察展开后的example函数，swith语句与while(1)语句以一种奇怪的方式嵌套：case 12的语句在while(1)循环之内。这是OK的。（[Ref1](#1)提到这个机制是实现C coroutine的关键。我们在达夫设备一节详细讨论这种机制。）这里用汇编语言来表示会更加容易白，因为在汇编语言中，C的流程控制语句，都会转换成值的比较和标签的跳转。因此switch和while的嵌套会更加容易理解。对由上述代码示例生成的汇编代码片段注释如下：

  ```assembly
  example:
  .LFB0:
          .cfi_startproc
          pushq   %rbp
          .cfi_def_cfa_offset 16
          .cfi_offset 6, -16
          movq    %rsp, %rbp
          .cfi_def_cfa_register 6
          subq    $16, %rsp
          movq    %rdi, -8(%rbp)
          movq    -8(%rbp), %rax
          movzwl  (%rax), %eax
          movzwl  %ax, %eax
          testl   %eax, %eax              ; 比较 example_pt->lc 是否为0
          je      .L3 					; 如果为0，跳转到 L3
          cmpl    $14, %eax 			    ; 比较是否为14，14是调用__LINE__宏的结果
          je      .L4  					; 如果为14， 跳转到 L4
          jmp     .L7  					; 跳转到 L7， 对应example函数展开后的第13行。
  .L3:                					; while(1), 对应example函数展开后第6行
          movq    -8(%rbp), %rax
          movw    $14, (%rax)
  .L4:                 					; 对应example函数展开后第8行
          movl    counter(%rip), %eax
          cmpl    $1000, %eax 			; 比较 counter 是否为1000
          je      .L5 					; 如果是， 跳转到 L5
          movl    $0, %eax
          jmp     .L6
  .L5:									; 对应example函数展开后第9行
          movl    $.LC0, %edi
          call    puts
          movl    $0, counter(%rip)
          jmp     .L3
  .L7:
          movq    -8(%rbp), %rax
          movw    $0, (%rax)
          movl    $2, %eax
  .L6:									; 对应example函数展开后第8行的return语句
          leave
          .cfi_def_cfa 7, 8
          ret
          .cfi_endproc
  .LFE0:
          .size   example, .-example
          .globl  main
          .type   main, @function
  ```


观察以上实现可知，protothread本质上就是利用一组宏构造具有特殊控制流程的C函数。

<br/>

**达夫设备**

达夫设备是串行复制的一种优化实现，由汤姆达夫在1983年11月提出。

考虑以下语言无关的，数组复制到寄存器的抽象代码：

```c
send(to, from, count)
register short *to, *from;
register count;
{
	do
		*to = *from++;
	while(--count>0);
}
```

完成整个过程，需要的**判断的次数为count**。

达夫做的优化如下：

```c
send(to, from, count)
register short *to, *from;
register count;
{
	register n = (count + 7) / 8;
	switch(count % 8) {
	case 0:	do {	*to = *from++;
	case 7:		*to = *from++;
	case 6:		*to = *from++;
	case 5:		*to = *from++;
	case 4:		*to = *from++;
	case 3:		*to = *from++;
	case 2:		*to = *from++;
	case 1:		*to = *from++;
		} while(--n > 0);
	}
}
```

利用优化后的代码，完成整个过程，仅需要 ```（count + 7) / 8```次比较。[Ref2](#2)提到

> 从速度上说，由于采用了[循环展开](https://zh.wikipedia.org/wiki/%E5%BE%AA%E7%8E%AF%E5%B1%95%E5%BC%80)技巧，使所需处理的分支数减少，从而降低了在处理分支时，中断与刷新[流水线](https://zh.wikipedia.org/wiki/%E6%B5%81%E6%B0%B4%E7%BA%BF_(%E8%AE%A1%E7%AE%97%E6%9C%BA))的巨大运算开销，因而相较于简单、直接的循环代码，这段代码的执行效率较高。

仔细观察，这段代码的switch和do-while语句嵌套的方式和protothread里example函数非常相似。C语言层面对此的支持描述如下<sup>[Ref2](#2)</sup>：

>一方面，C语言对`switch`语句的规范较松。在`switch`控制语句内，条件标号（`case`）可以出现在任意子语句之前，充作其前缀。若未加入`break`语句，则在`switch`语句在根据条件判定，跳转到对应标号，并开始执行后，控制流会无视其余条件标号限定，一直执行到switch嵌套语句的末尾，此即switch语句的“掉落”（**fall-through**）特性。
>
>另一方面，C语言对跳转到循环内部提供了支持，因而此处的switch/case语句便可跳转到循环内部。

<br/>

**C Coroutine**



#### Reference

1. <a id="1"></a>[Under the hood][uth]
2. <a id="2"></a>[达夫设备][dd]
3. <a id="3"></a>[Duff's Device][dsd]



[uth]: http://dunkels.com/adam/pt/expansion.html

[dd]: https://zh.wikipedia.org/wiki/%E8%BE%BE%E5%A4%AB%E8%AE%BE%E5%A4%87
[dsd]: http://groups-beta.google.com/group/comp.lang.c/msg/bb78298175c42411?dmode=source



