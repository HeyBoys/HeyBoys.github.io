---
layout: post
title: 'Hello World!'
date: 2018-06-28
categories: 技术
subtitle: 'Uva11636'
cover: '/assets/img/algorithm.jpeg'
tags: algorithm Uva11636
---

## 题目综述
### [UVa链接](https://uva.onlinejudge.org/index.php?option=com_onlinejudge&Itemid=8&page=show_problem&problem=2683)
你刚刚学会使用“printf("Hello World\n")”来向世界问好了，因此你非常兴奋，希望输出n条“Hello World"信息，但你还没有学习循环语句，因此只能通过复制/粘贴的方式用n条printf语句来解决。比如经过一次复制/粘贴后，一条语句会变成两条，再经过一次复制/粘贴后，两条语句会变成四条……至少需要复制/粘贴多少次，才能使语句的条数恰好为n。

输入n，输出最小复制/粘贴次数

## 解答
由于复制/粘贴可以只复制一部分语句，所以每次复制/粘贴时贪心选择全部，当n小于贪心选择出的k次复制/粘贴的所得到语句的上限时，即可通过调整最后一次粘贴数来得到n条语句，k即为所求。
```cpp
#include <cstdio>
int main(int argc, const char * argv[]) {
    int n;
    int a[15] = {1,2,4,8,16,32,64,128,256,512,1024,2048,4096,8192,16384};
    for(int l = 1;scanf("%d",&n) != EOF;l++)
    {
        if(n <= 0)break;
        int i;
        for(i = 0;;i++)
        {
            if(n <= a[i])break;
        }
        printf("Case %d: %d\n",l,i);
        
    }
    return 0;
}
```
