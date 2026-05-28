图论中，**单源最短路径** 问题是经典且重要的课题。本文将从适用场景、核心思路、时间复杂度等角度对比 **BFS**、**SPFA** 和 **Dijkstra** 三种算法，并给出堆优化 Dijkstra 以及链式前向星存图的具体实现。

---

## 一、三种算法的异同点

### 1. 相同点

- **目标一致**：求解从源点到其他所有节点的最短路径。
- **核心操作**：都基于 **松弛操作 (Relaxation)**  
  对于边 `(u → v)`，若 `dist[u] + w < dist[v]`，则更新 `dist[v]`。
- **依赖队列**：均使用队列（或优先队列）管理待处理节点。

### 2. 不同点对比表

| 维度           | BFS（广度优先搜索）                | SPFA（Shortest Path Faster Algorithm）     | Dijkstra（堆优化）                    |
| -------------- | ---------------------------------- | ------------------------------------------- | ------------------------------------- |
| **适用场景**   | 无权图 / 边权全相等                 | 带负权边但**无负环**的图                    | 边权全为**非负数** (≥0)               |
| **数据结构**   | 普通队列 (FIFO)                    | 普通队列 (可优化为双端队列)                  | 优先队列 (最小堆)                     |
| **处理负权**   | ❌ 不支持                          | ✅ 支持，但无法处理负环                      | ❌ 不支持                             |
| **节点处理次数**| 每个节点入队 **1次**               | 节点可**多次入队**（距离更新则重新入队）      | 每个节点出队 **1次**（确定后不再改变） |
| **时间复杂度** | O(V + E)                           | 平均 O(E)，最坏 O(VE)                       | O((V+E)logV)                          |

---

## 二、核心思路差异

三者本质区别在于**对边权的假设不同**，导致“选择下一个处理节点”的策略不同。

### 🔹 BFS – 等权图（边权相等）

- **假设**：所有边权相等（或无权，等价于边权1）。
- **结论**：最短路径 = 最少边数。
- **策略**：按层扩展，第一次到达节点时的距离即最短距离。
- **实现**：普通队列，每个节点只处理一次。

### 🔹 Dijkstra – 非负权图

- **假设**：边权 ≥ 0。
- **结论**：当前未处理节点中距离最小的节点，其距离已是最短（后续负权不可能出现）。
- **策略**：优先队列每次取出 `dist` 最小的节点，标记为已确定，然后松弛其邻边。
- **实现**：每个节点出队一次，时间复杂度 O((V+E)logV)。

### 🔹 SPFA – 允许负权（无负环）

- **假设**：图中可能有负权边，但无负环。
- **结论**：一个节点的距离可能被多次更新（负权边可能带来更短路径）。
- **策略**：普通队列，节点距离被更新则重新入队，直到队列为空。
- **注意**：若某节点入队超过 V 次，说明存在负环，算法失效。


## 三、Dijkstra 堆优化实现（链式前向星）

### 1. 链式前向星存图

链式前向星是一种静态邻接表，效率高且写法简洁。

```cpp
const int N = 1e5 + 5, M = 2e5 + 5;
struct Edge {
    int y, w, pre;   // 终点、权值、上一条边的编号
} e[M];
int last[N], elen = 0;

void ins(int x, int y, int w) {
    e[++elen] = {y, w, last[x]};
    last[x] = elen;
}

// 遍历从 x 出发的所有边
for (int k = last[x]; k; k = e[k].pre) {
    int y = e[k].y, w = e[k].w;
    // 松弛操作
}

```

### 2. 堆优化 Dijkstra 完整代码

```cpp
#include <bits/stdc++.h>
using namespace std;

const int N = 100010, M = 200010;
struct Edge { int y, w, pre; } e[M];
int last[N], elen = 0;
int dist[N];
bool vis[N];

void ins(int x, int y, int w) {
    e[++elen] = {y, w, last[x]};
    last[x] = elen;
}

struct Node {
    int x, d;   // 节点编号 和 当前距离
    bool operator<(const Node &n) const {
        return d > n.d;   // 小根堆 (优先队列默认大根)
    }
};

void dijkstra(int s) {
    memset(dist, 0x3f, sizeof(dist));
    dist[s] = 0;
    priority_queue<Node> q;
    q.push({s, 0});

    while (!q.empty()) {
        Node cur = q.top(); q.pop();
        int x = cur.x;
        if (vis[x]) continue;
        vis[x] = true;
    
        for (int k = last[x]; k; k = e[k].pre) {
            int y = e[k].y, w = e[k].w;
            if (dist[y] > dist[x] + w) {
                dist[y] = dist[x] + w;
                if (!vis[y]) q.push({y, dist[y]});
            }
        }
    }
}

int main() {
    // 建图后调用 dijkstra(s)
    return 0;
}
```

---

## 四、总结

| 算法         | 核心思想                          | 适用场景             | 能否处理负权 |
| ------------ | --------------------------------- | -------------------- | ------------ |
| **BFS**      | 层次扩展，首次到达即最短          | 无权图 / 边权相等    | ❌            |
| **Dijkstra** | 贪心 + 优先队列，每次确定最小节点 | 非负权图             | ❌            |
| **SPFA**     | 队列动态更新，节点可重复入队      | 含负权边但无负环的图 | ✅            |

> **选择建议**：  
> - 边权非负 → 用 **Dijkstra + 堆优化**（稳定高效）。  
> - 存在负权边但无负环 → 用 **SPFA**（或 Bellman‑Ford）。  
> - 无权图 → 直接 **BFS** 最简便。

---

## 附：链式前向星 + vector 版本

```cpp
struct Edge { int y, w, next; };
vector<Edge> e;
vector<int> head;

void ins(int x, int y, int w) {
    e.push_back({y, w, head[x]});
    head[x] = e.size() - 1;
}

// 遍历
for (int k = head[x]; k != -1; k = e[k].next) {
    int y = e[k].y, w = e[k].w;
}
```