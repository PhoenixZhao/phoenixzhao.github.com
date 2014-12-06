---
layout: post
title: 最小生成树Prim和Kruskal算法整理 
---

###问题
给定一个有权图，求该图的最小生成树MST，即所有的边权值之和最小。

###Prim算法
####算法过程

	for u in V
		color[u] = white
		key[u] = ∞
	key[r] = 0；pred[r] = NIL
	CreatePriorityQueue(V)
	while Q is not empty
		u = Extract-Min(V)
		for v in adj(u)
		if color[v] == white & key[v] > w[u, v]
			key[v] = w[u,v]
			pred[v] = u
			DecreasedQ(v, key[v])
		color[u] = black
		
####时间复杂度
和Dijkstra算法一样，都是O(ElogV),如果使用斐波纳挈堆，可以优化至O(E + VlogV)。

###Kruskal算法
####算法过程
kruskal算法的过程更加直接一些，只需要将所有的边排序，然后依次从小到大不停地选边，碰到产生循环的就扔掉，直到覆盖掉所有的点。

但由于需要判断新加入的边是否会造成循环，需要用一种新的数据结构--**并查集**，用来处理一些离散的点所属集合的查询问题。即已经加入的边的点作为一个集合，其余的点作为不同的集合，一旦发现加入的边的两个点和已经加入的点是同一个集合，则如果继续加入，将会产生循环。


	sorted all edges by weights
	for u in V, 
		createSet(v)
	for e in E:
		if find-set(u1) != find-set(u2)
			add edge e to A 
			union-set(u1,u2)
	return A
	

			