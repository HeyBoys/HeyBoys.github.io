---
layout: post
title: 'DNA Consensus String'
date: 2018-07-06
categories: 技术
subtitle: 'LA 3602'
cover: '/assets/img/algorithm.jpeg'
tags: algorithm LA3602
---

## 题目综述
### [LA链接](https://icpcarchive.ecs.baylor.edu/index.php?option=com_onlinejudge&Itemid=8&category=19&page=show_problem&problem=1603)
给定m个长度均为n的DNA序列，求一个DNA序列，使其到所有的总Hamming距离尽量小。两个等长字符串的Hamming距离等于字符不同的位置个数。如有多节，求字典序最小的解，输入m和n（4≤m≤50，4≤n≤1000），以及m个长度为n的DNA序列（只包含字母A，C，G，T），输出让Hamming距离最小的DNA序列和其对应距离。

## 解答
构造hamming距离最短的序列，只需选择每位出现最多的字符即可，存在相同时选取字典序最小的。
```cpp
#include <cstdio>
int main(int argc, const char * argv[]) {
    int l;
    scanf("%d",&l);
    for(int ll = 0;ll < l;ll++)
    {
        
        int m,n;
        scanf("%d %d",&m,&n);
        char c[50][1001] = {0};
        int sum[4][1001] = {0};
        for(int i = 0;i<m;i++)
            scanf("%s",c[i]);
        for(int i = 0;i<m;i++)
            for(int j = 0;j<n;j++)
            {
                switch (c[i][j])
                {
                    case 'A':
                        sum[0][j]++;
                        break;
                    case 'C':
                        sum[1][j]++;
                        break;
                    case 'G':
                        sum[2][j]++;
                        break;
                    default:
                        sum[3][j]++;
                        break;
                }
            }
        char ans[1001],table[4] ={'A','C','G','T'};
        int k = 0;
        for(int j = 0;j<n;j++)
        {
            int max = -1,maxid = 0;
            for(int i = 0;i<4;i++)
                if(sum[i][j] > max)
                {
                    max = sum[i][j];
                    maxid = i;
                }
            ans[j] = table[maxid];
            k += (m - max);
        }
        ans[n] = '\0';
        printf("%s\n%d\n",ans,k);
        
    }
    return 0;
}
```