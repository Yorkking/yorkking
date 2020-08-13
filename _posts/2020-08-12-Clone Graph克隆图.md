---
title: 克隆图
tags:
 - 算法
---

## 题目

#### [133. Clone Graph](https://leetcode-cn.com/problems/clone-graph/)

Given a reference of a node in a **[connected](https://en.wikipedia.org/wiki/Connectivity_(graph_theory)#Connected_graph)** undirected graph.

Return a [**deep copy**](https://en.wikipedia.org/wiki/Object_copying#Deep_copy) (clone) of the graph.

Each node in the graph contains a val (`int`) and a list (`List[Node]`) of its neighbors.

```
class Node {
    public int val;
    public List<Node> neighbors;
}
```



**Test case format:**

For simplicity sake, each node's value is the same as the node's index (1-indexed). For example, the first node with `val = 1`, the second node with `val = 2`, and so on. The graph is represented in the test case using an adjacency list.

**Adjacency list** is a collection of unordered **lists** used to represent a finite graph. Each list describes the set of neighbors of a node in the graph.

The given node will always be the first node with `val = 1`. You must return the **copy of the given node** as a reference to the cloned graph.



## 解法

这道题讲的就是如何深复制一个图，其实根本问题就是如何遍历整个图结构，并在遍历的过程中完成复制。图的遍历方式主要有两种：DFS 和 BFS。本来图的遍历相对树而言可能更复杂，但本题是根据图的一个 node 进而复制整个图，这个过程其实暗含了遍历的图必定是连通图，所以问题是大大被简化了。

思路就是：从给定的节点 node 出发进而 DFS 遍历，边遍历的过程中边复制；如果遇到一个节点已经被遍历了，那么直接返回该被访问的节点对应的复制节点：即需要使用一个 map 来标记访问的节点，并保存被访问节点对应的复制的节点。

代码如下：

```C++
/*
// Definition for a Node.
class Node {
public:
    int val;
    vector<Node*> neighbors;
    
    Node() {
        val = 0;
        neighbors = vector<Node*>();
    }
    
    Node(int _val) {
        val = _val;
        neighbors = vector<Node*>();
    }
    
    Node(int _val, vector<Node*> _neighbors) {
        val = _val;
        neighbors = _neighbors;
    }
};
*/

class Solution {
public:
    unordered_map<Node*,Node*> proxy_map;
    Node* cloneGraph(Node* node) {
        if(node == nullptr) return nullptr;
        if(proxy_map.find(node) != proxy_map.end()) return proxy_map[node];
        Node* res = new Node(node->val);
        proxy_map.emplace(node,res);
        for(auto& temp: node->neighbors){
            res->neighbors.push_back(cloneGraph(temp));
        }
        return res;
    }
};
```

这道题写完后，去评论区看了一下评论，看到一条很受启发的[评论](https://leetcode-cn.com/problems/clone-graph/solution/ke-long-tu-by-leetcode-solution/)：

> 课本上总说deep copy和shallow copy，似懂非懂的，不觉得这东西有什么用。慢慢地，发现deep copy背后隐藏的逻辑其实是一种对象图（Object Graph）的遍历行为——这东西广泛出现在各语言的垃圾回收、序列化机制里。内存里各个对象存储空间中放置的引用域/指针就好像有向图里一条边，你沿着它去到达内存中的每个角落、去到当前对象所有的关联对象。题设里的`neibours`就像一道开胃菜，它可以是其他collection、甚至object，学会这个deep copy，你也就学会了GC里的可达性分析、你也就学会了如何把RAM中的数据固化到硬盘里。

想到了上次字节面试时，面试官就问过深复制的相关问题，原来深复制背后有这样的运用！