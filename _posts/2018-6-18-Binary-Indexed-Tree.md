---
layout: post
title: 'BinaryIndexedTree(FenwickTree)'
date: 2018-06-18
categories: 技术
cover: '/assets/img/data_structure.jpg'
tags: 数据结构 
---

# 二叉索引树（树状数组）
## 1. 概述
给定一个n个元素的数组A[1……n]，采取一个数据结构支持更新update(x,d)：让Ax增加d，以及Query(L,R)计算A[L……R]部分和，则树状数组是最好的选择。
## 2. 数据结构
``` cpp
class BinaryIndexedTree
{
public:
	BinaryIndexedTree(int n,int *data);
	~BinaryIndexedTree();
	void update(int k, int x);
	int Query(int L, int R);
private:
	int n;
	int * c;
	inline int lowbit(int t);
	int sum(int x);
};
```
树状数组采用分治的思想，构造一颗记录前缀和的树

![](/assets/img/1(a).png)

C[i]代表 子树的叶子结点的权值之和// 这里以求和举例 如图可以知道 <br/>C[1]=A[1];<br/> C[2]=A[1]+A[2];<br/> C[3]=A[3]; <br/>C[4]=A[1]+A[2]+A[3]+A[4];<br/> C[5]=A[5]; <br/>C[6]=A[5]+A[6];<br/> C[7]=A[7];<br/> C[8]=A[1]+A[2]+A[3]+A[4]+A[5]+A[6]+A[7]+A[8];

即C[i]=A[i-2^k+1]+A[i-2^k+2]+......A[i]（k为i的二进制中从最低位到高位连续零的长度）

故引入lowbit(i)计算对于每个i所对应的2^k为多少
## 3.算法
### 3.1 BinaryIndexedTree(int n,int * data)
``` cpp
BinaryIndexedTree::BinaryIndexedTree(int n,int * data)
{
	this->n = n;
	c = new int[n + 1];
	for (int i = 1; i <= n; i++)
		c[i] = 0;
	for (int i = 1; i <= n; i++)
		update(i, data[i]);
}
```

树状数组的构造函数，用于分配内存空间以及初始化，即对一个空数组更新全部元素。
### 3.2 BinaryIndexedTree::sum(int x)
``` cpp
int BinaryIndexedTree::sum(int x)
{
	int ans = 0;
	for (int i = x;i; i -= lowbit(i))
		ans += c[i];
	return ans;
}
```

用于查询A[1……x]前缀和，区间查询的部分和可由前缀和得出。

下面利用C[i]数组，求A数组中前i项的和  

举个例子 i=7; <br/>sum[7]=A[1]+A[2]+A[3]+A[4]+A[5]+A[6]+A[7] ; <br/>前i项和 C[4]=A[1]+A[2]+A[3]+A[4]; <br/>C[6]=A[5]+A[6];<br/>C[7]=A[7];<br/>可以推出:   sum[7]=C[4]+C[6]+C[7]; <br/>序号写为二进制: sum[(111)]=C[(100)]+C[(110)]+C[(111)];

利用c数组计算前缀和，即每次减去当前下标的lowbit(i)，对应树状数组图中，顺着结点i向左上方走直到尽头。
### 3.3 update(int k, int x)
``` cpp
void BinaryIndexedTree::update(int k, int x)
{
	for (int i = k; i <= n; i += lowbit(i))
	{
		c[i] += x;
	}
}
```
用于更新A[i]结点，`A[i] = A[i] + x`

当更新A[i],可能要更新多个C数组结点 

举例当更新A[1]时  需要向上更新C[1] ,C[2],C[4],C[8] <br/>写为二进制  C[(001)],C[(010)],C[(100)],C[(1000)]  <br/>1(001) 　　　　　　　　　　　　　											C[1]+=A[1]  <br/>lowbit(1)=001 1+lowbit(1)=2(010)     				C[2]+=A[1]  <br/>lowbit(2)=010 2+lowbit(2)=4(100)     	C[4]+=A[1]  <br/>lowbit(4)=100 4+lowbit(4)=8(1000)   C[8]+=A[1]

改变A[i]时更新C数组，即每次加上当前下标的lowbit(i)，对应树状数组图中，顺着结点i向右上方走直到尽头。

