---
layout: post
title: 'Ancient Cipher!'
date: 2018-07-06
categories: 技术
subtitle: 'LA 3213'
cover: '/assets/img/algorithm.jpeg'
tags: algorithm LA3213
---

## 题目综述
### [LA链接](https://icpcarchive.ecs.baylor.edu/index.php?option=com_onlinejudge&Itemid=8&category=15&page=show_problem&problem=1214)
给定两个长度均为n的字符串，判断他们之间的26个字母是否能一一对应，即做一个一一映射后使得两个字符串相同（比如ABB和CDD可以一一对应，方法A→C,B→D但是ABC和DED不能一一对应）输入两个字符串，输出YES或者NO
输入n，输出最小复制/粘贴次数

## 解答
由于两个字符串之间可以调换顺序再进行转换，所以字符串的排序并不重要，只需要统计每个字符串由什么字母组成即可。将两个字符串统计后的数字排序后，一一对应则YES，否则NO
```cpp
#include <map>
#include <cstring>
#include <cstdio>
#include <algorithm>
using namespace std;
int main(int argc, const char * argv[]) {
    char a1[101],a2[101];
    for(int l = 0;scanf("%s\n%s",a1,a2) != EOF;l++)
    {
        int b1[26] ={0},b2[26]={0};
        size_t n = strlen(a1);
        for(int i = 0;i<n;i++)
        {
            b1[a1[i] - 'A']++;
            b2[a2[i] - 'A']++;
        }
        sort(b1, b1+26);
        sort(b2, b2+26);
        int i;
        for(i = 0;i<26;i++)
        {
            if(b1[i] != b2[i])break;
        }
        if(i == 26)printf("YES\n");
        else    printf("NO\n");
    }
    return 0;
}
```