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

####正确性证明
#####推论
证明prim的正确性需要用到一个推论

	令G=(V, E)是一个有权图，A是E的子集，并且A也是MST的子集。然后构造一个图的cut (S, V-S)，使得它respects A，即A中没有边cross这个cut。那么，如果(u,v)是（S, V-S）的light edge，则(u, v)是A的safe edge，即A + {(u, v)}也是MST的子集。
	几个概念的解释
	1.cut表示图的一个切分；
	2.如果一个cut respects A，即表示A中没有边cross这个cut；
	3.cross一个cut中的权值最小的边即为light edge;
	4.safe edge表示一个MST的子集加上这个边后，仍是MST的子集。
现在需要证明这个推论。设T是G的一个MST, 令A in T, 分两种情况讨论:

1,如果(u, v) in T, A + {(u, v)} 也在T中；

2,如果(u, v) not in MST。 

(a)来考虑在T中u-v的路径P，因为(u,v)是(S, V-S)的light edge，则P中至少有一条边cross(S, V-S)，

(b)设这条边为(x, y)，则w(u,v) <= w(x, y);

(c)将(u, v)加到T里，那么就会生成一个循环，然后再将(x,y)去掉，这样就生成了另外一个生成树T',由于w(u,v) <= w(x,y),则W(T') <= W(T);而T是MST，则W(T') = W(T)，即T'也是一个MST;

(d) A + {(u, v)} in T', MST；得证。

#####prim算法的正确性
开始构造一个集合S={r}，任意选一条边r，然后利用priorityQueue不断选择最小(S, V-S)light edge，不断加入到S中，仍然能保证S为MST的子集；最终算法完成，即可得到MST;

		
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
	
####正确性证明
设A(i)为处理完e_i后的集合，如果

(a)加入e_i+1生成循环，则A(i+1) = A(i);

(b)A(i+1) = A(i) + {e_i+1}。

需要证明，每一步，A(i) 都是MST T的子集；

使用数学归纳法来证明。显然i=0的时候，是成立的。假设A(i)成立；即A(i) in MST;对于case(a)， A(i+1)直接成立；需要证明case(b)；

定义T(i)包含所有的顶点集合，但T(i)表示已经执行kruskal的算法第i步时加入到该集合的边，那么T(0)表示n个孤立的点，也可以说是n个connected components，T(1)表示n-1个connected components,..., T(n)表示一个Connected components，也就是要求的MST。

对于case(b)，当加入e_i+1到T(i)中的时候，可以知道e_i+1中有一个顶点在T(i)中，而另一个不在(否则会产生循环),不失一般性，假设V1是其中的一个connected component，并且e_i+1有一个顶点在V1中, 即e_i+1 cross cut(V1, V-V1)，并且（V1, V-V1）respect A(i)。那么如果证明了e_i+1是cut(V1, V-V1)的light edge，也就证明了e_i+1是safe edge。

可以用反证法来证明e_i+1是safe edge。

假设e_i+1不是light edge。即存在一个j，e_j是（V1， V-V1）的light edge，即w(e_j) < w(e_i+1)，因此e_j不会在A(i)里产生循环,并且j < i+1，则A(j-1) 是A(i)的子集(kruskal算法做的)，并且由于e_j不会在A(i)里产生循环，那么此时e_j一定比e_i+1先加入到A(i)中，这与e_j是light edge 矛盾。