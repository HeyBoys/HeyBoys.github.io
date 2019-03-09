---
layout: post
title: '内存屏障及相关'
date: 2019-3-9
categories: 技术
cover: '/assets/img/storage.png'
tags:  storage
---
## “过度”的优化
由于处理器性能与编译器软件的逐步发展，出现了多种优化策略，以到达更多的运行速度。乱序执行就是其中一项很有效的优化性能的策略。但硬件器材由单核cpu变成多核时，由DRAM变成NVM非易失性内存时，过度的优化会导致程序的正确执行。
```java
//thread 1
while(!flag);
do(x);
//thread 2
x = 100;
flag = true;
```

### 1.编译器乱序执行
编译器会根据代码中语句的相关性进行重拍，以达到提升性能的作用，而由于编译器无法兼顾多线程的相关性，或者需要按顺序持久化存储数据时，乱序执行会带来问题

Linux内核中提供了barrier宏来解决编译器乱序执行问题

```java
#define barrier() __asm__ __volatile__("":::"memory")
```
barrier宏内联一个带memory标志的空指令汇编，memory标志告诉GCC编译器：

第一，不要将此标志前的指令与此指令重新排序，也就是在执行此条指令时，它前面的指令全部执行完毕。

第二，不要将此条指令中的变量缓存到寄存器，必须将缓存在寄存器的变量写入内存中，并将此寄存器失效。如果后面又访问这个变量，需要重新读内存。
这样就保证了避免乱序执行带来的逻辑错误
### 2.运行期乱序执行
现代cpu为了提高内部逻辑原件的利用率以提升运行速度，会使用乱序执行的策略，当前一条指令由于相关性无法执行时，后面已经准备好的指令可以抢先流入流水线进行执行，由于处理器检查的相关性只会检查当前cpu所执行的代码，所以也会引起多核与持久性的问题

在x86_64体系结构下，提供了一下几种指令提供此一致性

lfence:load barrier读屏障，使得前面所有读指令执行完毕再执行后面的指令

sfence:write barrier写屏障 使得前面所有的写指令执行完毕再执行后面的指令

mfence：兼具lfence与sfence，使前面所有的访存指令执行完毕后再执行后面的指令

带Lock前缀的访存指令，cpu执行Lock前缀的访存时，会锁总线，在这之前会把之前的读写操作结束，功能相当于mfence

## 正确的使用
Linux内核中，具有mb(),rmb(),wmb()几个宏加入内存屏障

在X86_64体系下:
```java
#define mb() __asm__ __volatile__("mfence":::"memory")
```
在ia386体系下：
```java
#define mb() __asm__ __volatile__("lock;addl &0,0(%%esp)":::"memory")
```
上文代码应修改成
```java
//thread 1
while(!flag);
do(x);
//thread 2
x = 100;
__asm__ __volatile__("mfence":::"memory")；：
flag = true;
```