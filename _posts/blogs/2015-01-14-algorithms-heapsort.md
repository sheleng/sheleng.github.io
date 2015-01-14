---
layout: blog-post
title: 堆排序
autor: Sheleng
category: blogs
tags: [sort,heap,algorithms]
description: 
---

###堆的数据结构

(二叉)堆的数据结构是一种数组对象，但是其可以被视为一棵完全二叉树
（如下图所示）。树的每一层都是填满的，最后一层可能除外。

![](/public/images/posts/blogs/2015-01-14-algorithms-heapsort/heap-array-binary-tree.jpg)

在树中，圆圈内的数字表示每个结点存储的值，结点上方的数字表示该结点在数组中的下标。

表示堆的数组A具有两个属性：

- length[A]是数组中的元素个数；
- heap_size是存放在A中的堆的元素个数。

其中，0 <= heap_size <= length[A]

在这里假设数组下标为1开始，树的根为A[1]，给定某个结点的下标`i`，可以通过以下方式简单的计算出`i`结点的父亲`parent（i）`，左儿子`left_child（i）`和右儿子`right_child（i）`的下标：

	parent(i)
	  return i / 2

	left_child(i)
	  return 2 * i

	right_child(i)
	  return (2 * i) + 1

二叉堆有两种：最大堆和最小堆。

- 最大堆特性是指除了根结点以外的每个结点`i`，有：`A[parent(i)] >= A[i]`

- 最小堆的组织方式则相反；最小堆特性是指除了根结点以外的每个结点`i`，有：`A[parent(i)] <= A[i]`

###堆性质的保持

以最大堆为例：输入为一个数组`A`和下标`i`，假设以`i`结点为根的两棵二叉树都是最大堆。

- 从元素`A[i]`结点、`A[left_child(i)]`和`A[right_child(i)]`中找出最大值，并其下标保存在`largest`中。
- 如果`largest == i`，则说明以`i`为根的子树已是最大堆，调整结束；否则，说明`i`结点不符合最大堆性质，需交换`A[i]`和`A[largest]`,交换后，可能致使以`largest`为根的子树违反最大堆的性质，因而要对该子树进行递归调整。

伪代码如下：

	max_heap_fix(A,i,heap_size)
	  l <- left_child(i)
	  r <- right_child(i)
	  largest <- i
	  if l <= heap_size and A[l] > A[i]
	    then largest = l;
	  if r <= heap_size and A[r] > A[i]
		then largest = r;
	  if largest != i
		then exchange A[i] <-> A[largest]
		     max_heap_fix(A,largest,heap_size)

`max_heap_fix(A,i,heap_size)`的时间复杂度为`O(lgn)`

###建堆
- 子数组`A[(n/2)+1 ... n]`中的元素都是树中的叶子结点。因此可以看作是只含一个元素的堆。

- 对子数组`A[1 ... (n/2)]`对结点`i`由`（n/2）`到 `1`依次调用`max_heap_fix(A,i,heap_size)`进行调整，使得每个结点都符合最大堆的性质。

伪代码如下：

	build_max_heap(A)
	  heap_size <- length(A)
	  for i <- lenght[A] / 2 downto 1
	    do max_heap_fix(A,i,heap_size)

`build_max_heap(A)`的时间复杂度为`O(n)`

###堆排序算法
通过`build_max_heap(A)`就可以将数组`A`调成一个最大堆。

1、因为数组中最大元素是在根`A[1]`，则可以通过把它与`A[n]`互换来达到正确的位置（升序）。

2、并且通过`heap_size = heap_size - 1`将结点`n`从堆中移出。

3、交换元素后，原来根的子女仍是最大堆，而新的根元素可能违背最大堆性质，因此需要调用`max_heap_fix(A,1,heap_size)`来保存这一性质。

4、重复步骤1，直到堆的大小由`n-1`一直降到`2`。

伪代码如下：

	heap_sort(A)
	  heap_size <- length(A)
	  build_max_heap(A)
	  for i <- length(A) - 1 downto 2
	    do exchange A[1] <-> A[i]
		   heap_size = heap_size - 1
		   max_heap_fix(A,1,heap_size)

`heap_sort(A)`的时间复杂度为`O(nlgn)`