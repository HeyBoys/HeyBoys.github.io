---
layout: post
title: 'Watering Grass'
date: 2018-07-09
categories: 技术
subtitle: 'UVa 10382'
cover: '/assets/img/algorithm.jpeg'
tags: algorithm UVa10382
---

## 题目综述
### [UVa链接](https://uva.onlinejudge.org/index.php?option=com_onlinejudge&Itemid=8&page=show_problem&problem=1323)
给一块草坪，长为l,宽为w。在其中心线的不同位置处装有n个点状的喷水装置。每个喷水装置i可将以它为中心，半径为ri的圆形区域润湿。请选择尽量少的喷水装置，把整个草坪全部润湿

## 解答
输入数据为每个装置点的半径与位置，而要求满足整个矩形区域。故在输入预处理为，将其转化为每个喷水装置能满足的矩形区域。问题则转化为一个最小的区域覆盖的问题
```cpp
#include <iostream>
#include <cstdio>
#include <algorithm>
#include <math.h>
using namespace std;
struct interval
{
    float left,right;
}interval;
bool compare(struct interval a,struct interval b)
{
    return a.left < b.left;
}
int main(int argc, const char * argv[]) {
    int n,l,w;
    while(scanf("%d %d %d",&n,&l,&w) != EOF)
    {
        struct interval a[10001];
        int k  = 0;
        for(int i = 0;i < n;i++)
        {
            int p,r;
            scanf("%d %d",&p,&r);
            if(2 * r >= w)
            {
                float d =sqrt((float)r*(float)r - ((float)w/2)*((float)w/2));
                a[k].left = p - d;
                a[k].right =p + d;
                k++;
            }
        }
        n = k;
        sort(a, a+n, compare);
        float k1 = 0;
        int j,u = -1;
        for(j = 0;k1 < l;j++)
        {
            float max = -10000;
            int maxid = 0;
            for(int i = u+1;i<n;i++)
            {
                if(a[i].left >= k1)
                    break;
                if(a[i].right >max)
                {
                    max = a[i].right;
                    maxid = i;
                }
            }
            if(max == -10000)
                break;
            k1 = max;
            u = maxid;
        }
        if(k1 >= l)printf("%d\n",j);
        else printf("-1\n");
    }
    return 0;
}

```