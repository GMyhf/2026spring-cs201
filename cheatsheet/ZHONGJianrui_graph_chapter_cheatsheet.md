# 8 图

> 期末范围提示：PDF 强调图的表示、BFS/DFS、拓扑排序、SCC、MST、最短路、关键路径；SMT 等扩展内容不优先掌握。

## 8.2 建图模板

鲁棒性建图注意事项：

- 先确认点编号是 `0..n-1` 还是 `1..n`；1-indexed 时通常开 `n+1`，循环也从 `1` 到 `n`。
- 先确认有向/无向：无向边要加两次，有向边只加一次；入度/出度不要写反。
- 重边：邻接表可保留，BFS/DFS 用 `visited` 一般不影响正确性，但会增加遍历次数；邻接矩阵/最短路常保留最小权值；拓扑依赖若语义是“同一依赖只算一次”，要先去重再统计入度。
- 自环：会直接构成有向/无向环，二部图中自环必不合法；MST、正权最短路中通常可忽略；若题目统计边数/度数，不能随意删。
- 无向图做桥/割边时如果有重边，DFS 里不要只用 `v == parent` 跳过父节点，最好给每条边编号，用 `edge_id` 跳过反向边。

按题意去重：

```python
seen = set()
for u, v in edges:
    key = (u, v) if directed else (min(u, v), max(u, v))
    if key in seen:
        continue
    seen.add(key)
    # 再建图 / 统计入度
```

无权邻接表：

```python
def build_graph(n, edges, directed=False):
    graph = [[] for _ in range(n)]
    for u, v in edges:
        graph[u].append(v)
        if not directed:
            graph[v].append(u)
    return graph
```

带权邻接表：

```python
def build_weighted_graph(n, edges, directed=False):
    # edges = [(u, v, w), ...]
    graph = [[] for _ in range(n)]
    for u, v, w in edges:
        graph[u].append((v, w))
        if not directed:
            graph[v].append((u, w))
    return graph
```

邻接矩阵：

```python
def build_matrix(n, edges, directed=False, weighted=False):
    INF = float('inf')
    mat = [[INF] * n for _ in range(n)]
    for i in range(n):
        mat[i][i] = 0

    for edge in edges:
        if weighted:
            u, v, w = edge
        else:
            u, v = edge
            w = 1
        mat[u][v] = min(mat[u][v], w)  # 处理重边
        if not directed:
            mat[v][u] = min(mat[v][u], w)
    return mat
```

度数统计：

```python
def undirected_degree(n, edges):
    deg = [0] * n
    for u, v in edges:
        deg[u] += 1
        deg[v] += 1
    return deg

def directed_degree(n, edges):
    indeg = [0] * n
    outdeg = [0] * n
    for u, v in edges:
        outdeg[u] += 1
        indeg[v] += 1
    return indeg, outdeg
```

## 8.3 BFS 广度优先搜索

应用场景：

- 无权图最短路。
- 层号、最少步数、迷宫最短路。
- 连通块扩展。
- 拓扑排序的 Kahn 算法。

```python
from collections import deque

def bfs(graph, start):
    n = len(graph)
    dist = [-1] * n
    parent = [-1] * n
    q = deque([start])
    dist[start] = 0

    while q:
        u = q.popleft()
        for v in graph[u]:
            if dist[v] == -1:
                dist[v] = dist[u] + 1
                parent[v] = u
                q.append(v)

    return dist, parent
```

还原最短路径：

```python
def restore_path(parent, s, t):
    if s == t:
        return [s]
    if parent[t] == -1:
        return []

    path = []
    cur = t
    while cur != -1:
        path.append(cur)
        if cur == s:
            break
        cur = parent[cur]

    return path[::-1] if path[-1] == s else []
```

网格 BFS：

```python
from collections import deque

def grid_bfs(grid, sx, sy):
    m, n = len(grid), len(grid[0])
    dist = [[-1] * n for _ in range(m)]
    q = deque([(sx, sy)])
    dist[sx][sy] = 0
    dirs = [(1, 0), (-1, 0), (0, 1), (0, -1)]

    while q:
        x, y = q.popleft()
        for dx, dy in dirs:
            nx, ny = x + dx, y + dy
            if 0 <= nx < m and 0 <= ny < n:
                if grid[nx][ny] != '#' and dist[nx][ny] == -1:
                    dist[nx][ny] = dist[x][y] + 1
                    q.append((nx, ny))

    return dist
```

## 8.4 DFS 深度优先搜索

应用场景：

- 判断连通性、统计连通块。
- 找一条路径、回溯搜索。
- 判环。
- 拓扑排序、强连通分量。

```python
import sys
sys.setrecursionlimit(10 ** 6)

def dfs_components(graph):
    n = len(graph)
    visited = [False] * n
    comps = []

    def dfs(u, comp):
        visited[u] = True
        comp.append(u)
        for v in graph[u]:
            if not visited[v]:
                dfs(v, comp)

    for i in range(n):
        if not visited[i]:
            comp = []
            dfs(i, comp)
            comps.append(comp)

    return comps
```

迭代版 DFS：

```python
def dfs_iter(graph, start):
    visited = [False] * len(graph)
    stack = [start]
    order = []

    while stack:
        u = stack.pop()
        if visited[u]:
            continue
        visited[u] = True
        order.append(u)
        for v in reversed(graph[u]):
            if not visited[v]:
                stack.append(v)

    return order
```

DFS 时间戳：`dfn` 是发现时间，`finish` 是结束时间；结束时间逆序可用于 DFS 拓扑排序和 SCC。

```python
def dfs_timestamp(graph):
    n = len(graph)
    dfn = [0] * n
    finish = [0] * n
    timer = 0

    def dfs(u):
        nonlocal timer
        timer += 1
        dfn[u] = timer
        for v in graph[u]:
            if not dfn[v]:
                dfs(v)
        timer += 1
        finish[u] = timer

    for i in range(n):
        if not dfn[i]:
            dfs(i)

    return dfn, finish
```

## 8.5 判环

无向图 DFS 判环，关键是排除父节点：

```python
def has_cycle_undirected(graph):
    n = len(graph)
    visited = [False] * n

    def dfs(u, parent):
        visited[u] = True
        for v in graph[u]:
            if not visited[v]:
                if dfs(v, u):
                    return True
            elif v != parent:
                return True
        return False

    for i in range(n):
        if not visited[i] and dfs(i, -1):
            return True
    return False
```

有向图三色标记判环：

```python
def has_cycle_directed(graph):
    n = len(graph)
    state = [0] * n  # 0 未访问，1 正在访问，2 已完成

    def dfs(u):
        state[u] = 1
        for v in graph[u]:
            if state[v] == 1:
                return True
            if state[v] == 0 and dfs(v):
                return True
        state[u] = 2
        return False

    for i in range(n):
        if state[i] == 0 and dfs(i):
            return True
    return False
```

桥 / 关键连接：无向图中删除后会使图不连通的边。判断条件：DFS 树边 `u-v` 满足 `low[v] > dfn[u]`。

```python
def find_bridges(n, edges):
    graph = [[] for _ in range(n)]
    for u, v in edges:
        graph[u].append(v)
        graph[v].append(u)

    dfn = [0] * n
    low = [0] * n
    timer = 0
    bridges = []

    def dfs(u, parent):
        nonlocal timer
        timer += 1
        dfn[u] = low[u] = timer

        for v in graph[u]:
            if v == parent:
                continue
            if not dfn[v]:
                dfs(v, u)
                low[u] = min(low[u], low[v])
                if low[v] > dfn[u]:
                    bridges.append((u, v))
            else:
                low[u] = min(low[u], dfn[v])

    for i in range(n):
        if not dfn[i]:
            dfs(i, -1)

    return bridges
```

## 8.6 拓扑排序

适用对象：DAG。若无法得到长度为 `n` 的拓扑序，说明有环。

Kahn 算法：

```python
from collections import deque

def topo_sort(n, graph):
    indeg = [0] * n
    for u in range(n):
        for v in graph[u]:
            indeg[v] += 1

    q = deque([i for i in range(n) if indeg[i] == 0])
    order = []

    while q:
        u = q.popleft()
        order.append(u)
        for v in graph[u]:
            indeg[v] -= 1
            if indeg[v] == 0:
                q.append(v)

    return order if len(order) == n else None
```

总是选择编号最小的可选点，用堆：

```python
import heapq

def topo_sort_min(n, graph):
    indeg = [0] * n
    for u in range(n):
        for v in graph[u]:
            indeg[v] += 1

    heap = [i for i in range(n) if indeg[i] == 0]
    heapq.heapify(heap)
    order = []

    while heap:
        u = heapq.heappop(heap)
        order.append(u)
        for v in graph[u]:
            indeg[v] -= 1
            if indeg[v] == 0:
                heapq.heappush(heap, v)

    return order if len(order) == n else None
```

DFS 拓扑排序：

```python
def dfs_topo(graph):
    n = len(graph)
    state = [0] * n
    order = []

    def dfs(u):
        state[u] = 1
        for v in graph[u]:
            if state[v] == 1:
                return False
            if state[v] == 0 and not dfs(v):
                return False
        state[u] = 2
        order.append(u)  # 结束时间顺序
        return True

    for i in range(n):
        if state[i] == 0 and not dfs(i):
            return None

    return order[::-1]
```

DAG 上的 DP：先拓扑排序，再按拓扑序转移。常见于 DAG 最长路、课程/任务依赖累计、方案数统计。

```python
from collections import deque

def dag_longest_path(n, edges, sources=None):
    # edges = [(u, v, w), ...]，求从 sources 出发的最长距离
    graph = [[] for _ in range(n)]
    indeg = [0] * n
    for u, v, w in edges:
        graph[u].append((v, w))
        indeg[v] += 1

    original_indeg = indeg[:]
    q = deque([i for i in range(n) if indeg[i] == 0])
    topo = []

    while q:
        u = q.popleft()
        topo.append(u)
        for v, _ in graph[u]:
            indeg[v] -= 1
            if indeg[v] == 0:
                q.append(v)

    if len(topo) != n:
        return None  # 不是 DAG

    if sources is None:
        sources = [i for i in range(n) if original_indeg[i] == 0]

    NEG = -float('inf')
    dp = [NEG] * n
    parent = [-1] * n
    for s in sources:
        dp[s] = 0

    for u in topo:
        if dp[u] == NEG:
            continue
        for v, w in graph[u]:
            if dp[u] + w > dp[v]:
                dp[v] = dp[u] + w
                parent[v] = u

    return dp, parent
```

如果是 DAG 最短路，把 `NEG` 改成 `INF`，初值改成 `inf`，转移条件改成 `<`。

## 8.7 最短路选择

| 问题 | 算法 |
|---|---|
| 无权图最短路 | BFS |
| 非负权单源最短路 | Dijkstra |
| 有负权边，需判负环 | Bellman-Ford / SPFA |
| 多源最短路，点数较小 | Floyd-Warshall |
| DAG 上最短/最长路 | 拓扑序 DP |
| 不等式约束系统 | 差分约束 + Bellman-Ford/SPFA |

## 8.8 Dijkstra

适用：非负权图的单源最短路。负权边会破坏“当前最近点已确定”的贪心性质。

```python
import heapq

def dijkstra(n, graph, start):
    # graph[u] = [(v, w), ...]
    INF = float('inf')
    dist = [INF] * n
    parent = [-1] * n
    dist[start] = 0
    pq = [(0, start)]

    while pq:
        d, u = heapq.heappop(pq)
        if d != dist[u]:
            continue  # 堆中的旧记录

        for v, w in graph[u]:
            nd = d + w
            if nd < dist[v]:
                dist[v] = nd
                parent[v] = u
                heapq.heappush(pq, (nd, v))

    return dist, parent
```

如果只求 `s -> t`，第一次弹出 `t` 时即可返回：

```python
def dijkstra_to_t(n, graph, s, t):
    import heapq
    dist = [float('inf')] * n
    dist[s] = 0
    pq = [(0, s)]

    while pq:
        d, u = heapq.heappop(pq)
        if d != dist[u]:
            continue
        if u == t:
            return d
        for v, w in graph[u]:
            if d + w < dist[v]:
                dist[v] = d + w
                heapq.heappush(pq, (dist[v], v))

    return -1
```

差分约束常用转化：

```text
x_v - x_u <= w  转成边  u -> v, 权重 w
求最大差值      常转成最短路上界
```

## 8.9 Bellman-Ford

适用：有负权边的单源最短路，可检测负权环。复杂度 `O(VE)`。

```python
def bellman_ford(n, edges, start):
    # edges = [(u, v, w), ...]
    INF = float('inf')
    dist = [INF] * n
    dist[start] = 0

    for _ in range(n - 1):
        updated = False
        for u, v, w in edges:
            if dist[u] != INF and dist[u] + w < dist[v]:
                dist[v] = dist[u] + w
                updated = True
        if not updated:
            break

    for u, v, w in edges:
        if dist[u] != INF and dist[u] + w < dist[v]:
            return None  # 存在从 start 可达的负权环

    return dist
```

差分约束：把形如 `x[a] - x[b] <= c` 的不等式转成边 `b -> a`，权重为 `c`。若存在负权环，则约束系统无解；否则 `dist` 给出一组可行解。

```python
def difference_constraints(n, constraints):
    # constraints = [(a, b, c), ...] 表示 x[a] - x[b] <= c
    source = n
    edges = []

    # 超级源点连向所有变量，保证每个点都可达
    for i in range(n):
        edges.append((source, i, 0))

    for a, b, c in constraints:
        edges.append((b, a, c))

    dist = [float('inf')] * (n + 1)
    dist[source] = 0

    for _ in range(n):
        updated = False
        for u, v, w in edges:
            if dist[u] != float('inf') and dist[u] + w < dist[v]:
                dist[v] = dist[u] + w
                updated = True
        if not updated:
            break

    # 第 n+1 轮还能松弛，说明存在负环，约束无解
    for u, v, w in edges:
        if dist[u] != float('inf') and dist[u] + w < dist[v]:
            return None

    return dist[:n]
```

常见转化：

```text
x[a] - x[b] <= c    ->  b -> a, weight = c
x[a] - x[b] >= c    ->  a -> b, weight = -c
x[a] - x[b] == c    ->  同时加入 <= c 和 >= c
```

## 8.10 SPFA

SPFA 是 Bellman-Ford 的队列优化，只松弛“刚被更新过的点”的出边。能处理负权边，但最坏复杂度仍是 `O(VE)`。

```python
from collections import deque

def spfa(n, graph, start):
    INF = float('inf')
    dist = [INF] * n
    inq = [False] * n
    cnt = [0] * n

    dist[start] = 0
    q = deque([start])
    inq[start] = True
    cnt[start] = 1

    while q:
        u = q.popleft()
        inq[u] = False

        for v, w in graph[u]:
            if dist[u] + w < dist[v]:
                dist[v] = dist[u] + w
                if not inq[v]:
                    cnt[v] += 1
                    if cnt[v] >= n:
                        return None  # 负权环
                    q.append(v)
                    inq[v] = True

    return dist
```

## 8.11 Floyd-Warshall

适用：所有点对最短路，点数较小。复杂度 `O(n^3)`，支持负权边，但不支持负权环。

```python
def floyd(mat):
    # mat[i][j] 是 i 到 j 的初始距离，不可达为 inf
    n = len(mat)
    dist = [row[:] for row in mat]

    for k in range(n):          # 中间点必须在最外层
        for i in range(n):
            if dist[i][k] == float('inf'):
                continue
            for j in range(n):
                if dist[k][j] != float('inf'):
                    dist[i][j] = min(dist[i][j], dist[i][k] + dist[k][j])

    has_neg_cycle = any(dist[i][i] < 0 for i in range(n))
    return dist, has_neg_cycle
```

## 8.12 并查集 DSU

适用：动态维护连通关系，常用于连通块合并、Kruskal、防止成环、等价关系分组。下面是带 `size/members` 的版本，不使用 `rank`；如果题目不需要取集合成员，可以删掉 `members` 相关代码省内存。

```python
class DSU:
    def __init__(self, n):
        self.parent = list(range(n))
        self.size = [1] * n
        self.members = [{i} for i in range(n)]

    def find(self, x):
        if self.parent[x] != x:
            self.parent[x] = self.find(self.parent[x])  # 路径压缩
        return self.parent[x]

    def union(self, a, b):
        ra, rb = self.find(a), self.find(b)
        if ra == rb:
            return False

        # 小集合并入大集合，降低成员搬移总次数
        if self.size[ra] < self.size[rb]:
            ra, rb = rb, ra
        self.parent[rb] = ra
        self.size[ra] += self.size[rb]
        self.members[ra].update(self.members[rb])
        self.members[rb].clear()
        return True

    def same(self, a, b):
        return self.find(a) == self.find(b)

    def get_size(self, x):
        return self.size[self.find(x)]

    def get_members(self, x):
        return self.members[self.find(x)]
```

并查集判断是否为二部图：对每个点拆成两个状态，`x` 表示一种颜色，`x+n` 表示相反颜色。边 `(u, v)` 要求 `u` 和 `v` 异色，因此合并 `u` 与 `v+n`，合并 `u+n` 与 `v`。

```python
def is_bipartite_dsu(n, edges):
    # edges 是无向图边集 [(u, v), ...]
    dsu = DSU(2 * n)

    for u, v in edges:
        # 如果 u 和 v 已经被迫同色，则不能二分
        if dsu.same(u, v):
            return False

        # 约束：u 与 v 异色
        dsu.union(u, v + n)
        dsu.union(u + n, v)

    return True
```

食物链：带权并查集，关系对 `3` 取模。约定 `rel[x]` 表示 `x` 到根的关系：`0` 同类，`1` 表示 `x` 吃根，`2` 表示 `x` 被根吃。两个点关系为 `(rel[x] - rel[y]) % 3`。

```python
class FoodDSU:
    def __init__(self, n):
        self.parent = list(range(n + 1))
        self.rel = [0] * (n + 1)

    def find(self, x):
        if self.parent[x] != x:
            p = self.parent[x]
            self.parent[x] = self.find(p)
            self.rel[x] = (self.rel[x] + self.rel[p]) % 3
        return self.parent[x]

    def add(self, x, y, r):
        # r: 0 同类；1 x吃y；2 x被y吃
        rx, ry = self.find(x), self.find(y)
        if rx == ry:
            return (self.rel[x] - self.rel[y]) % 3 == r
        self.parent[rx] = ry
        self.rel[rx] = (r + self.rel[y] - self.rel[x]) % 3
        return True

def count_lies(n, statements):
    # statements: (t, x, y)，t=1 同类，t=2 x吃y
    dsu = FoodDSU(n)
    lies = 0
    for t, x, y in statements:
        if x < 1 or x > n or y < 1 or y > n or (t == 2 and x == y):
            lies += 1
            continue
        r = 0 if t == 1 else 1
        if not dsu.add(x, y, r):
            lies += 1
    return lies
```

## 8.13 最小生成树 MST

适用：无向连通带权图，找连接所有点且总权重最小的树。

注意：

- MST 有 `n - 1` 条边。
- MST 可能不唯一，但最小总权重唯一。
- 有负权边也可以使用 Prim/Kruskal。
- Prim 更像从点扩张，Kruskal 更像从边集合中选边。

Prim，适合稠密图或邻接表已给出：

```python
import heapq

def prim(n, graph, start=0):
    # graph[u] = [(v, w), ...]
    visited = [False] * n
    pq = [(0, start)]
    total = 0
    count = 0

    while pq and count < n:
        w, u = heapq.heappop(pq)
        if visited[u]:
            continue
        visited[u] = True
        total += w
        count += 1

        for v, cost in graph[u]:
            if not visited[v]:
                heapq.heappush(pq, (cost, v))

    return total if count == n else None
```

Kruskal，适合稀疏图：

```python
def kruskal(n, edges):
    # edges = [(w, u, v), ...]
    edges.sort()
    dsu = DSU(n)
    total = 0
    count = 0

    for w, u, v in edges:
        if dsu.union(u, v):
            total += w
            count += 1
            if count == n - 1:
                break

    return total if count == n - 1 else None
```

## 8.14 强连通分量 SCC

适用：有向图中找互相可达的“团”。常见后续操作是缩点，把每个 SCC 看成一个点，得到 DAG。

Kosaraju 思路：

1. 对原图 DFS，记录完成时间。
2. 建转置图。
3. 按完成时间逆序在转置图 DFS，每棵 DFS 树是一个 SCC。

Tarjan 缩点 + 统计缩点 DAG 入/出度模板。适合类似 “Network of Schools” 的题：

- `cnt`：强连通分量个数。
- `id[u]`：原点 `u` 所在的 SCC 编号。
- `in_res`：缩点 DAG 中入度为 0 的 SCC 数量，常表示至少需要从几个点/学校开始。
- `max(in_res, out_res)`：若要让所有 SCC 互相可达，至少需要补的边数；如果 `cnt == 1`，答案为 0。

```python
import sys
sys.setrecursionlimit(10**6)

n = int(input())
graph = [[] for _ in range(n + 1)]
for i in range(1, n + 1):
    a = list(map(int, input().split()))
    graph[i] = a[:-1]          # 输入行末有终止符时去掉，例如 0

dfn = [0] * (n + 1)
low = [0] * (n + 1)
st = []
idx = [0] * (n + 1)             # id[u] = u 所在 SCC 编号
flag = [False] * (n + 1)       # 是否仍在栈中
timer = 0
cnt = 0

def tarjan(u):
    global timer, cnt
    timer += 1
    dfn[u] = low[u] = timer
    st.append(u)
    flag[u] = True

    for v in graph[u]:
        if not dfn[v]:
            tarjan(v)
            low[u] = min(low[u], low[v])
        elif flag[v]:
            low[u] = min(low[u], dfn[v])

    # dfn[u] == low[u] 表示 u 是一个 SCC 的根
    if dfn[u] == low[u]:
        cnt += 1
        while st:
            x = st.pop()
            flag[x] = False
            idx[x] = cnt
            if x == u:
                break

for i in range(1, n + 1):
    if not dfn[i]:
        tarjan(i)

in_deg = [False] * (cnt + 1)
out_deg = [False] * (cnt + 1)

# 扫描原图边，统计缩点 DAG 中每个 SCC 是否有入边/出边
for u in range(1, n + 1):
    for v in graph[u]:
        if idx[u] != idx[v]:
            out_deg[idx[u]] = True
            in_deg[idx[v]] = True

in_res = sum(1 for k in range(1, cnt + 1) if not in_deg[k])
out_res = sum(1 for k in range(1, cnt + 1) if not out_deg[k])

print(in_res)
print(0 if cnt == 1 else max(in_res, out_res))
```

洛谷 最优贸易
```python
import sys
sys.setrecursionlimit(10**6)
from collections import deque
input = sys.stdin.buffer.readline

def main():
    n, m = map(int, input().split())
    graph = [[] for _ in range(n+1)]
    price = [0]+list(map(int, input().split()))
    for _ in range(m):
        x, y, z = map(int, input().split())
        graph[x].append(y)
        if z == 2:
            graph[y].append(x)

    dfn = [0]*(n+1)
    low = [0]*(n+1)
    st = []
    max_p = [0]
    min_p = [0]
    flag = [False]*(n+1)
    idx = [0]*(n+1)
    timer = 0
    cnt = 0
    def tarjan(u):
        nonlocal timer, cnt
        timer += 1
        dfn[u] = low[u] = timer
        st.append(u)
        flag[u] = True
        for v in graph[u]:
            if not dfn[v]:
                tarjan(v)
                low[u] = min(low[u], low[v])
            elif flag[v]:
                low[u] = min(low[u], dfn[v])
        if dfn[u] == low[u]:
            cnt += 1
            M = -float('inf')
            L = float('inf')
            while st:
                x = st.pop()
                M = max(price[x], M)
                L = min(price[x], L)
                flag[x] = False
                idx[x] = cnt
                if x == u:
                    break
            max_p.append(M)
            min_p.append(L)
    for i in range(1, n+1):
        if not dfn[i]:
            tarjan(i)

    edges = [set() for _ in range(cnt+1)]
    edges2 = [set() for _ in range(cnt+1)]
    for u in range(1, n+1):
        for v in graph[u]:
            if idx[u] != idx[v]:
                edges[idx[u]].add(idx[v])
                edges2[idx[v]].add(idx[u])
    in_deg = [len(edges2[i]) for i in range(cnt+1)]
    value = [float('inf')]*(cnt+1)
    prize = [-float('inf')]*(cnt+1)
    q = deque()
    topo = []
    for i in range(1, cnt+1):
        if not in_deg[i]:
            q.append(i)
    while q:
        u = q.popleft()
        topo.append(u)
        for v in edges[u]:
            in_deg[v] -= 1
            if not in_deg[v]:
                q.append(v)
    value[idx[1]] = min_p[idx[1]]
    for u in topo:
        if value[u] == float('inf'):
            continue
        for v in edges[u]:
            value[v] = min(value[v], value[u], min_p[v])
    prize[idx[n]] = max_p[idx[n]]
    for u in reversed(topo):
        if prize[u] == -float('inf'):
            continue
        for v in edges2[u]:
            prize[v] = max(prize[v], prize[u], max_p[v])
    print(max(prize[i]-value[i] for i in range(cnt+1)))

if __name__ == '__main__':
    main()
```

## 8.15 关键路径 AOE 网

适用：DAG 上的工程调度。边表示活动，边权表示耗时。关键路径就是从源点到汇点的最长路径。

核心公式：

```text
ve[v] = max(ve[v], ve[u] + w(u, v))       # 最早发生时间
vl[u] = min(vl[u], vl[v] - w(u, v))       # 最晚发生时间
边 (u, v, w) 是关键活动 <=> ve[u] == vl[v] - w
```

模板：

```python
from collections import deque

def critical_path(n, edges):
    # edges = [(u, v, w), ...]，要求 DAG；最好有单源单汇
    graph = [[] for _ in range(n)]
    indeg = [0] * n
    for u, v, w in edges:
        graph[u].append((v, w))
        indeg[v] += 1

    q = deque([i for i in range(n) if indeg[i] == 0])
    topo = []
    ve = [0] * n

    while q:
        u = q.popleft()
        topo.append(u)
        for v, w in graph[u]:
            ve[v] = max(ve[v], ve[u] + w)
            indeg[v] -= 1
            if indeg[v] == 0:
                q.append(v)

    if len(topo) != n:
        return None  # 有环，不是合法 AOE 网

    project_time = max(ve)
    vl = [project_time] * n

    for u in reversed(topo):
        for v, w in graph[u]:
            vl[u] = min(vl[u], vl[v] - w)

    critical_edges = []
    for u, v, w in edges:
        if ve[u] == vl[v] - w:
            critical_edges.append((u, v, w))

    return project_time, critical_edges, ve, vl
```

## 8.16 常见题型对照

| 题目关键词 | 常用方法 |
|---|---|
| 无权最短路、最少步数、层数 | BFS |
| 找任意一条路、连通块、回溯 | DFS |
| 依赖关系、课程表、DAG | 拓扑排序 |
| 有向图是否有环 | 三色 DFS / Kahn |
| 无向图关键连接/桥 | Tarjan：`low[v] > dfn[u]` |
| 判断二分图/二部图 | DSU 拆点 |
| 非负权最短路 | Dijkstra |
| 负权边、负环 | Bellman-Ford / SPFA |
| 不等式约束、最大差值上界 | 差分约束 |
| 任意两点最短路，点数小 | Floyd |
| 连接所有点总成本最小 | MST：Prim / Kruskal |
| MST 后保留若干连通分量 | Kruskal 做 `n-s` 次合并 |
| 有向图互相可达分组 | SCC：Tarjan / Kosaraju |
| SCC 缩点后统计源/汇分量 | Tarjan + SCC 入出度 |
| DAG 最长路、方案数、依赖累计 | 拓扑序 DP |
| 工期最短时间、关键活动 | AOE 关键路径 |
