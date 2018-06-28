---
layout: post
title: 'Building Designing'
date: 2018-06-28
categories: 技术
subtitle: 'Uva11039'
cover: '/assets/img/algorithm.jpeg'
tags: algorithm Uva11039
---

## 题目综述
### [UVa链接](https://uva.onlinejudge.org/index.php?option=com_onlinejudge&Itemid=8&page=show_problem&problem=1980)
有n个绝对值各不相同的非0整数，选出尽量多的数，排成一个序列，使得正负号交替，且绝对值递增。输入整数n(1≤n≤500000)和n个整数，输出最长序列数

## 解答
由于需要正负号交替出现，可以将使用两个数组分别存储正数与负数。要求序列尽可能的长且绝对值递增，则每个放入的数的绝对值应尽可能的小。故将两个数组排序后，每次选取对应数组第一个绝对值大于上一个数绝对值的即可。
```cpp
#include <algorithm>
#include <vector>
#include <cstdio>
using namespace std;
#define maxn 500000
bool cmp(int &a,int &b)
{
    return a>b;
}
int main(int argc, const char * argv[]) {
    int l;
    scanf("%d",&l);
    for(int ll = 0;ll<l;ll++)
    {
        int n;
        vector<int>a,b;
        scanf("%d",&n);
        if (n == 0)
        {
            printf("0\n");
            continue;
        }
        for(int i = 0;i<n;i++)
        {
            int x;
            scanf("%d",&x);
            if(x>0)a.push_back(x);
            else b.push_back(x);
        }
        if(a.size() == 0 || b.size() == 0)
        {
            printf("1\n");
            continue;
        }
        sort(a.begin(), a.end());
        sort(b.begin(), b.end(),cmp);
        int a_i = 0,b_i = 0;
        unsigned int k;
        bool flag;
        if(a[0] > -b[0])
        {
            flag = false;
            k = -b[0];
            b_i = 1;
        }
        else
        {
            flag = true;
            k = a[0];
            a_i = 1;
        }
        int line = 1;
        for(int i = 1;i<n;i++)
        {
            if(flag)
            {
                for(;b_i<b.size();b_i++)
                    if(-b[b_i] > k)break;
                if(b_i == b.size())
                    break;
                line++;
                k = - b[b_i];
                flag = false;
            }
            else
            {
                for(;a_i<a.size();a_i++)
                    if(a[a_i] > k)break;
                if(a_i == a.size())
                    break;
                line++;
                k = a[a_i];
                flag = true;
            }
        }
        printf("%d\n",line);
    }
    return 0;
}
```
