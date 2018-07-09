---
layout: post
title: 'Children’s Game'
date: 2018-07-09
categories: 技术
subtitle: 'UVa 10905'
cover: '/assets/img/algorithm.jpeg'
tags: algorithm UVa10905
---

## 题目综述
### [UVa链接](https://uva.onlinejudge.org/index.php?option=com_onlinejudge&Itemid=8&page=show_problem&problem=1846)
给定n个正整数，你的任务是把它们连接成一个最大的整数。比如123、124、56、90有24种连接方式，最大的结果是9056124123

## 解答
当前若存在一个最大结果时，可知对于任何选取两个相邻的数字a、b,a+b必定大于b+a。故按照此方法排序即可。

```cpp
#include <iostream>
#include <cstdio>
#include <vector>
#include <string>
#include <sstream>
#include <algorithm>
using namespace std;
bool compare(string a,string b)
{
    return a+b > b+a;
}
int main(int argc, const char * argv[]) {
    int n;
    while (true)
    {
        scanf("%d",&n);
        if(n == 0)break;
        string a[51];
        for(int i = 0;i<n;i++)
        {
            cin >> a[i];
        }
        sort(a, a+n, compare);
        for(int i = 0;i<n;i++)
            cout <<a[i];
        cout <<endl;
    }
    return 0;
}
```