# Algorithms

## LeetCode 题解模板

````markdown
[TOC]

## 题目解读



## 解法 1

### 数学原理



### 算法与数据结构



### 参考代码

``` c++
#include <iostream>

class Solution {
public:

};

int main() {
    std:: cout << Solution(). << std:: endl;

    return 0;
}

```

### 复杂度分析

- 时间复杂度：$O(*)$，；
- 空间复杂度：$O(*)$，；

````

## Graph Algorithms

### Single-Source Shortest Paths

#### Dijkstra's Algorithm

We assume that $w(u, v) \geq 0$ for each edge $(u, v)\in E$

```pseudocode
DIJKSTRA. (G,w,s)
1 INITIALIZE-SINGLE-SOURCE(G,s)
2 S=∅
3 Q=G.V  // Q: min-priority queue
4 while Q≠∅
5 	u=D EXTRACT-MIN(Q)
6 	S=S∪{u}
7 	for each vertex v∈G.Adj[u]
8 		RELAX(u,v,w)
```

```c++
void dijkstra(vector<vector<pair<int, int>>> &graph, int src) {
    int n = graph.size();
    vector<int> dist(n, INT_MAX);
    // Sorted by distance, the vertices with smaller distance are ranked first.
    priority_queue<pair<int, int>, vector<pair<int, int>>,
                   greater<pair<int, int>>>
        pq; // Store the vertices to be processed and their distance from the
            // source point

    pq.push({0, src});
    dist[src] = 0;

    // O((V+E)logV)
    while (!pq.empty()) {
        int u = pq.top().second;
        pq.pop();

        for (auto it : graph[u]) {
            int v = it.first;
            int weight = it.second;

            if (dist[v] > dist[u] + weight) {
                dist[v] = dist[u] + weight;
                pq.push({dist[v], v});
            }
        }
    }

    for (int i = 0; i < n; ++i) {
        cout << i << "\t" << dist[i] << endl;
    }
}

```

```c++
// Test Code
#include <climits> // for INT_MAX
#include <iostream>
#include <queue>
#include <utility> // for pair
#include <vector>

using namespace std;

void dijkstra(vector<vector<pair<int, int>>> &graph, int src);

int main() {
    // Adjacency table
    vector<vector<pair<int, int>>> graph = {
        {{1, 4}, {2, 3}}, {{2, 1}, {3, 2}}, {{3, 4}}, {}};
    int src = 0;
    dijkstra(graph, src);

    return 0;
}

void dijkstra(vector<vector<pair<int, int>>> &graph, int src) {
    // ...
}
```

## Reference

[1] C. E. Leiserson, T. H. Cormen, R. L. Rivest, and C. Stein, "Introduction to Algorithms, 3rd Edition," MIT Press, 2009.
