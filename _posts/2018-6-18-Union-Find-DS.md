---
layout: post
title: 'Union-Find DS'
date: 2018-06-18
categories: 技术
cover: '/assets/img/data_structure.jpg'
tags: 数据结构 
---

# 并查集
## 1.概述
并查集是一种描述不相交集合的数据结构，设有一个动态集合S={s1，s2，s3，.....sn}，每个集合通过一个代表元来标识，代表元就是动态集合Si中的某个元素。并查集数据结构可实现快速查找某一元素所在集合，以及集合之间的合并。

**多用于处理无向图的连通分量，某两顶点是否想通，以及最小生成树**
## 2.数据结构
``` cpp
class union_find
{
public:
	union_find(int n);
	~union_find();
	int FindSet(int i);
	bool IsSameSet(int i, int j);
	void UnionSet(int i, int j);
private:
	int n;
	int *parent,*rank;
};

``` 

并查集采用树形结构进行存储，为每个集合建立一棵树，集合的代表元即为此树的根节点。整个并查集为一个森林。

并查集采用两个一维数组存储每个结点的信息。parent[i]代表第i个结点的父亲结点号，当此结点为根结点时指向自己。rank[i]代表以i为根节点的树的高度。
## 3.算法
### 3.1 union_find(int n)
``` cpp
union_find::union_find(int n)
{
	this->n = n;
	parent = new int[n];
	rank = new int[n];
	for (int i = 0; i < n; i++)
	{
		parent[i] = i;
		rank[i] = 0;
	}
}
```

并查集的构造函数，用于分配内存空间以及初始化
### 3.2 FindSet(int i)
``` cpp
int union_find::FindSet(int i)
{
	if (parent[i] == i)
		return i;
	else 
	return parent[i] = FindSet(parent[i]); 
}
```

返回i号节点所在集合的代表元。

**路径压缩**

为了加快查找速度，查找时将i到根节点路径上的所有点的parent设为根节点，该优化方法称为压缩路径。使用该优化后，平均复杂度可视为Ackerman函数的反函数，实际应用中可粗略认为其是一个常数。

![](/assets/img/3.2(a).png) 

                                并查集的树形结构3.2(a)

![](/assets/img/3.2(b).png)

                                路径压缩后的树形结构3.2(b)

### 3.3 IsSameSet(int i, int j)
``` cpp
bool union_find::IsSameSet(int i, int j)
{
	return FindSet(i) == FindSet(j);
}
```

判断i,j两个节点是否在统一集合内
### 3.4 UnionSet(int i, int j)
``` cpp
void union_find::UnionSet(int i, int j)
{
	int x1, x2;
	x1 = FindSet(i);
	x2 = FindSet(j);
	if (x1 == x2) return;
	if (rank[x1] > rank[x2]) parent[x2] = x1;
	else
	{
		if (rank[x1] == rank[x2]) rank[x2]++;
		parent[x1] = x2;
	}
}
```

将i所在集合与j所在集合合并

对于合并操作，假设需要合并的两个集合的代表元分别为x,y，则只需要`parent[x] = y `或者`parent[y] = x`即可。为了使合并后的树不产生退化，应使树中左右子树的深度差尽可能小，对于每一个元素x，维护rank[x]为以x为根的子树的深度。合并时，如果`rank[x]<rank[y]`，则令`parent[x] = y`，否则令`parent[y] = x`.

![](/assets/img/3.4(a).png)　　　　

                                合并前的树形结构3.4(a)　

![](/assets/img/3.4(b).png)  

                                合并后的树形结构3.4(b)