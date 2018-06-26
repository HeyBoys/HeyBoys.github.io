---
layout: post
title: 'Segment-tree'
date: 2018-06-18
categories: 技术
tags: 数据结构 
---

# 线段树
## 1.概述
假设有编号从1到n的n个点，每个点都存了一些信息，用[L,R]表示下标从L到R的这些点。

线段树的用处就是，对编号连续的一些点进行修改或者统计操作，修改和统计的复杂度都是O(log2(n)).

线段树的原理，就是，将[1,n]分解成若干特定的子区间(数量不超过4*n),然后，将每个区间[L,R]都分解为 少量特定的子区间，通过对这些少量子区间的修改或者统计，来实现快速对[L,R]的修改或者统计。

由此看出，用线段树统计的东西，必须符合区间加法，否则，不可能通过分成的子区间来得到[L,R]的统计结果。

**符合区间加法,可以使用线段树储存**

数字之和——总数字之和 = 左区间数字之和 + 右区间数字之和

最大值/最小值——总最大值/最小值=max(左区间最大值/最小值，右区间最大值/最小值)
## 2.数据结构
``` cpp
struct Segment_Tree_Node
{
public:
	int val = 0;
	int addMark = 0;
};
class Segment_Tree
{
public:
	Segment_Tree(int n);
	~Segment_Tree();
	void build(int val[]);
	int query(int start, int end);
	void update(int start, int end, int addVal);

private:
	int n;
	Segment_Tree_Node * segTree;
	const int INFINITE = 0X7FFFFFFF;
	inline int min(int a, int b);
	void pushDown(int root);
	void do_build(int root, int val[], int start, int end);
	void do_update(int root, int nstart, int nend, int ustart, int uend, int addVal);
	int do_query(int root, int nstart, int nend, int qstart, int qend);

};

```

线段树是一颗完全二叉树，每个结点代表一段区间，包含两个域，分别保存每个结点的值与延迟标记。线段树将每个区间[L,R]分解成[L,M]和[M+1,R] (其中M=(L+R)/2)直到 L==R 为止。

下图为一颗长度为13的树

![](/assets/img/2(a).png)
## 3.算法
### 3.1 void pushDown(int root)
``` cpp
void Segment_Tree::pushDown(int root)
{
	if (segTree[root].addMark != 0)
	{
		segTree[root * 2 + 1].addMark += segTree[root].addMark;
		segTree[root * 2 + 2].addMark += segTree[root].addMark;
		segTree[root * 2 + 1].val += segTree[root].addMark;
		segTree[root * 2 + 2].val += segTree[root].addMark;
		segTree[root].addMark = 0;
	}
}
```
当用户修改一个区间的值时，如果连同其子孙全部修改，则改动的节点数必定会远远超过O(log n)个。所以为每个结点引入延迟标记域，把对节点的修改情况储存在标记里面，使得每次修改一段区间时，可以不改变其孩子结点。当真正访问孩子节点时再跟随一次更新

引入pushDown函数，负责将当前结点的传递到其孩子结点
### 3.2 do_build(int root, int val[], int start, int end)
``` cpp
void Segment_Tree::do_build(int root, int val[], int start, int end)
{
	segTree[root].addMark = 0;
	if (start == end)
		segTree[root].val = val[start];
	else
	{
		int mid = (start + end) / 2;
		do_build(root * 2 + 1, val, start, mid);
		do_build(root * 2 + 2, val, mid + 1, end);
		segTree[root].val = min(segTree[root * 2 + 1].val, segTree[root * 2 + 2].val);
	}
}
```

线段树的初始化函数，采用递归方式，每次递归构造左右子树再合并得出该结点的值。
### 3.3 do_update(int root, int nstart, int nend, int ustart, int uend, int addVal)
``` cpp
void Segment_Tree::do_update(int root, int nstart, int nend, int ustart, int uend, int addVal)
{
	if (ustart > nend || uend < nstart)
		return;
	if (ustart <= nstart && uend >= nend)
	{
		segTree[root].val += addVal;
		segTree[root].addMark += addVal;
		return;
	}
	pushDown(root);
	int mid = (nstart + nend) / 2;
	do_update(root * 2 + 1, nstart, mid, ustart, uend, addVal);
	do_update(root * 2 + 2, mid + 1, nend, ustart, uend, addVal);
	segTree[root].val = min(segTree[root * 2 + 1].val, segTree[root * 2 + 2].val);
}
```
线段树的区间更新函数，nstart~nend为结点区间，ustart~uend为更新区间

当前结点区间为更新区间的子区间时，更新该节点数值并设置延迟标识并对其子结点更新，否则传递延迟标记并分别递归左右子树寻找表示区间为更新区间的子区间的结点进行更新，返回时更新父节点。
### 3.4 do_query(int root, int nstart, int nend, int qstart, int qend)
``` cpp
int Segment_Tree::do_query(int root, int nstart, int nend, int qstart, int qend)
{
	if (qstart > nend || qend < nstart)
		return INFINITE;
	if (qstart <= nstart && qend >= nend)
		return segTree[root].val;
	pushDown(root);
	int mid = (nstart + nend) / 2;
	return min(do_query(root * 2 + 1, nstart, mid, qstart, qend), do_query(root * 2 + 2, mid + 1, nend, qstart, qend));
}
```

线段树的区间查询函数，nstart~nend为结点区间，ustart~uend为查询区间
当前结点区间为查询区间的子区间时，返回该结点的值，否则传递延迟标记分别递归左右子树寻找表示区间为查询区间的子区间的结点，返回其值。

