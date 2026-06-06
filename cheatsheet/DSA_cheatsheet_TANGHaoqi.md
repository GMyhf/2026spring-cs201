## 一、图论

1、有向图拓扑排序+判环：
（1）Kahn算法（BFS)：统计入度并将入度为0的点入队，弹出时加入结果列表并将其邻居入度减一并检测邻居入度，重复直至队列为空。若结果集顶点数小于总点数则存在环。输出结果为拓扑序。示例如下：

```python
def topological_sort_kahn(n, adj):
    indegree = defaultdict(int)
    for u in adj:
        for v in adj[u]:
            indegree[v] += 1
    queue = deque([i for i in adj if indegree[i] == 0])
    result = []
    while queue:
        u = queue.popleft()
        result.append(u)
        for v in adj[u]:
            indegree[v] -= 1
            if indegree[v] == 0:
                queue.append(v)
    return result if len(result) == n else None  # None 表示有环
```
（2）DFS三色标记法：(输出逆拓扑序)
```python
def dfs_topo_sort(graph):
    # 状态字典：0 = 未访问(white), 1 = 正在访问(gray), 2 = 已访问完毕(black)
    visited = {v: 0 for v in graph}
    finish_order = []  # 记录顶点的结束顺序（先结束的放前⾯）
    def dfs_visit(u):
        visited[u] = 1  # 标记为正在访问
        for v in graph.get(u, []):
            if visited[v] == 0:
                if not dfs_visit(v):
                    return False  # 发现环
            elif visited[v] == 1:
                return False  # 发现环（指向了祖先节点）
        
        visited[u] = 2  # 标记为已访问完毕（相当于设置了 Finish Time）
        finish_order.append(u)  # 节点结束时，直接追加到结果列表中
        return True
    # 遍历所有顶点（处理⾮连通图或森林）
    for u in graph:
        if visited[u] == 0:
            if not dfs_visit(u):
                raise ValueError("图中存在环，⽆法进⾏拓扑排序")
    # 结束时间递减顺序排列，即为结束顺序的【逆序】
    return finish_order[::-1]
```

2、无向图判环：
（1）并查集模板：（适合动态加边，适合稠密图；用于Kruskal最小生成树）
```python
class UnionFind:
    def __init__(self, n):
        self.parent = list(range(n))
        self.rank = [0] * n
        #带权：self.weight = [0] * n  # weight[x] 表示 x 到 parent[x] 的权值
        self.count = n #连通分量个数
    
    def find(self, x):
        if self.parent[x] != x:
            root = self.find(self
            .parent[x])
            #带权：self.weight[x] += self.weight[self.parent[x]]  # 累加路径上的权值
            self.parent[x] = root
        return self.parent[x]
    
    def union(self, x, y):#如果带权还需输入权值w
        rx, ry = self.find(x), self.find(y)
        if rx == ry:
            return False
        if self.rank[rx] < self.rank[ry]:
            rx, ry = ry, rx
        self.parent[ry] = rx
        #带权：self.weight[ry] = self.weight[x] + w - self.weight[y]
        if self.rank[rx] == self.rank[ry]:
            self.rank[rx] += 1
        self.count -= 1
        return True
```
（2）Parent指针（DFS)：(适合静态图)
```python
def has_cycle_undirected(graph):
    visited = set()
    def dfs(node, parent):
        visited.add(node) # 进⻔先打卡
        for neighbor in graph[node]: # 挨个看邻居
            if neighbor not in visited: # 情况 A：没去过这个邻居
                # 递归进去，同时告诉邻居：我是你的 parent
                if dfs(neighbor, node):
                    return True
            # 情况 B：去过这个邻居，且这个邻居不是“领我进⻔”的那个⼈
            elif neighbor != parent:
                return True # 这就是环！
        return False
    # 处理森林情况（即图可能是断开的⼏部分，每个部分都要检查）
    for node in graph:
        if node not in visited:
            if dfs(node, -1): # 起始节点的 parent 设为 -1
                return True
    return False
```

3、SCC：
Tarjan_SCC算法模板：(输出为逆拓扑序)
```python
def tarjan_scc(graph):
    n = len(graph)
    dfn = [-1] * n      # 访问顺序时间戳
    low = [-1] * n      # 能追溯到的最⼩ dfn
    stack = []          # 遍历中的节点收集栈
    on_stack = [False] * n
    sccs = []
    timer = 0
    def dfs(u):
        nonlocal timer
        dfn[u] = low[u] = timer
        timer += 1
        stack.append(u)
        on_stack[u] = True
        for v in graph[u]:
            if dfn[v] == -1:    # 场景1：未访问过的树枝
                dfs(v)
                low[u] = min(low[u], low[v]) # 回溯时更新
            elif on_stack[v]:   # 场景2：已访问且在当前路径中（回边）
                low[u] = min(low[u], dfn[v])
        # 判定：如果 u 是 SCC 的根
        if low[u] == dfn[u]:
            component = []
            while True:
                node = stack.pop()
                on_stack[node] = False
                component.append(node)
                if node == u: break
            sccs.append(component)
    for i in range(n):
        if dfn[i] == -1: dfs(i)
    return sccs
```
下一步建图：
```python
def build_scc_graph(sccs, n, edges):
    # 节点到SCC编号的映射
    scc_id = [0] * n
    for i, comp in enumerate(sccs):
        for v in comp:
            scc_id[v] = i
    
    m = len(sccs)
    dag = [set() for _ in range(m)]  # 邻接表去重
    in_deg = [0] * m
    out_deg = [0] * m
    
    for u, v in edges:
        uid, vid = scc_id[u], scc_id[v]
        if uid != vid and vid not in dag[uid]:
            dag[uid].add(vid)
            out_deg[uid] += 1
            in_deg[vid] += 1
    
    return dag, in_deg, out_deg
"""无向图建图"""
def build_cc_graph(comp_id, weighted_edges,cc_num):
    comp_adj = [[] for _ in range(cc_num)]
    for u, v, w in weighted_edges:
        cu, cv = comp_id[u], comp_id[v]
        if cu != cv:
            comp_adj[cu].append((cv,w))
            comp_adj[cv].append((cu,w))
    return comp_adj  """可以直接接prim"""
    
```

注：无向图连通分量可以用并查集或BFS/DFS寻找，方法很简单，找到一个未出现过的节点就一直遍历邻居即可。（补图的最小生成树需要用连通分量解决）

4、Dijkstra：（单源最短路径+非负权边）
```python
import heapq
def dijkstra(n, graph, start): #邻接表建图
    # 初始化距离，起点为0，其余无穷大
    dist = [float('inf')] * n
    dist[start] = 0
    # 优先队列：(当前距离, 节点)
    pq = [(0, start)]
    while pq:
        d, u = heapq.heappop(pq)
        # 跳过已过时的记录
        
        if d > dist[u]:
            continue
        # 松弛邻居
        for v, w in graph[u]:
            if dist[u] + w < dist[v]:
                dist[v] = dist[u] + w
                heapq.heappush(pq, (dist[v], v))
    return dist
```

5、B-F算法+SPFA：（带负权边+检测负权回路）
```python
def bellman_ford(n, edges, start):
    # edges = [(u, v, w), ...]
    dist = [float('inf')] * n
    dist[start] = 0
    for _ in range(n - 1):
        for u, v, w in edges:
            if dist[u] != float('inf') and dist[u] + w < dist[v]:
                dist[v] = dist[u] + w
    # 负环检测
    for u, v, w in edges:
        if dist[u] + w < dist[v]:
            return "存在负权环"
    return dist
def spfa(n, edges, start):
    # 建邻接表
    adj = [[] for _ in range(n)]
    for u, v, w in edges:
        adj[u].append((v, w))
    dist = [float('inf')] * n
    dist[start] = 0
    in_queue = [False] * n   # 标记节点是否在队列中
    cnt = [0] * n            # 统计入队次数，用于负环检测
    q = deque([start])
    in_queue[start] = True
    while q:
        u = q.popleft()
        in_queue[u] = False
        for v, w in adj[u]:
            if dist[u] + w < dist[v]:
                dist[v] = dist[u] + w
                if not in_queue[v]:
                    q.append(v)
                    in_queue[v] = True
                    cnt[v] += 1
                    if cnt[v] >= n:       # 入队 n 次说明有负环
                        return "存在负权环"
    return dist
```
6、Floyd-Warshall算法+路径记录（带负权边+多源最短路径+邻接矩阵）
```python
ways=defaultdict(list)
def floyd_warshall(mat,P):
    matrix=[row[:] for row in mat]
    for k in range(P):
        for i in range(P):
            for j in range(P):
                if matrix[i][k]!=float('inf') and matrix[k][j]!=float('inf'):
                    if matrix[i][j]>matrix[i][k]+matrix[k][j]:
                        ways[(i,j)]=ways[(i,k)]+[k]+ways[(k,j)]
                        matrix[i][j]=matrix[i][k]+matrix[k][j]
```

7、最小生成树：
（1）prim算法：随机取点为起点加入heap与visited，用heap找出连接visited与unvisited的边集合中最小边并加入。注意只有在出堆时才能加入visited，并且需要立刻检查visited（环路）
示例：(存边版本)
```python
def prim_mst(graph, start_node):
    mst_edges = []      # 存储 MST 的边 (u, v, weight)
    visited = set()     # 已加⼊⽣成树的节点
    min_heap = [(0, start_node, None)]  # (权重, 当前节点, ⽗节点)
    total_weight = 0
    while min_heap:
        weight, curr_node, parent = heapq.heappop(min_heap)
        # 1. 检查是否形成环路（⽬标点是否已访问）
        if curr_node in visited:
            continue
        # 2. 将节点加⼊⽣成树
        visited.add(curr_node)
        total_weight += weight
        if parent is not None:
            mst_edges.append((parent, curr_node, weight))
        # 3. 扩展邻居节点
        for neighbor, edge_weight in graph[curr_node].items():
            if neighbor not in visited:
                heapq.heappush(min_heap, (edge_weight, neighbor, curr_node))
    return mst_edges, total_weight
```
（2）Kruskal:(并查集)
边少的时候用：
```python
def kruskal(n, edges):
    # edges = [(w, u, v), ...]
    edges.sort() # 按权重排序
    uf = UnionFind(n)
    mst_weight = 0
    count = 0
    for w, u, v in edges:
        if uf.union(u, v):
            mst_weight += w
            count += 1
    return mst_weight if count == n - 1 else "不连通"
```

8、关键路径（AOE网）计算最早最晚发生时间，可以确定松弛时间为0的关键活动。
```python
import sys
from collections import defaultdict, deque
class Edge:
    def __init__(self, v, w):
        self.v = v  # 边的终点
        self.w = w  # 边的权重（活动耗时）
def topo_sort(n, G, in_degree):
    """
   执⾏拓扑排序并计算每个事件的最早开始时间 ve
   """
    # 查找所有⼊度为 0 的点作为起点
    q = deque([i for i in range(n) if in_degree[i] == 0])
    ve = [0] * n """最早发生时间"""
    topo_order = []
    while q:
        u = q.popleft()
        topo_order.append(u)
        for edge in G[u]:
            v = edge.v
            # 更新最早开始时间：ve[v] = max(ve[v], ve[u] + weight)
            if ve[u] + edge.w > ve[v]:
                ve[v] = ve[u] + edge.w
            in_degree[v] -= 1
            if in_degree[v] == 0:
                q.append(v)
    # 判环：如果拓扑序列⻓度不等于 N，说明有环（AOE⽹不能有环）
    if len(topo_order) == n:
        return ve, topo_order
    else:
        return None, None
def get_critical_path(n, G, in_degree):
    # 步骤 1: 拓扑排序求 ve (最早发⽣时间)
    ve, topo_order = topo_sort(n, G, in_degree)
    if ve is None:
        return -1, None
    # 项⽬总⼯期即为 ve 中的最⼤值
    maxLength = max(ve)
    # 步骤 2: 反向推导求 vl (最晚发⽣时间)
    # 初始化所有节点的最晚发⽣时间为总⼯期
    vl = [maxLength] * n
    # 按照拓扑序列的逆序进⾏更新
    for u in reversed(topo_order):
        for edge in G[u]:
            v = edge.v
            # 更新最晚开始时间：vl[u] = min(vl[u], vl[v] - weight)
            if vl[v] - edge.w < vl[u]:
                vl[u] = vl[v] - edge.w
    # 步骤 3: 寻找关键活动并构建关键图
    # 关键活动定义：活动的 e[i] == l[i]
    # 即对应边 <u, v> 满⾜：ve[u] == vl[v] - weight
    activity = defaultdict(list)
    for u in range(n):
        for edge in G[u]:
            v = edge.v
            e = ve[u]  # 活动最早开始时间
            l = vl[v] - edge.w  # 活动最晚开始时间
            if e == l:
                activity[u].append(v)
    return maxLength, activity
def print_critical_path(u, activity, current_max, path=None):
    if path is None:
        path = []
    path.append(u)
    # 终⽌条件：如果没有从 u 出发的关键活动，说明到达路径末端
    if u not in activity or not activity[u]:
        print(" -> ".join(map(str, path)))
    else:
        # 按节点编号排序输出，保证结果稳定性
        for v in sorted(activity[u]):
            print_critical_path(v, activity, current_max, path.copy())
    path.pop()
```

9、欧拉路径：
```python
def euler_path(edges, n, directed=True):
    # 建图
    g = [[] for _ in range(n)]
    for u, v in edges:
        g[u].append(v)
        if not directed:
            g[v].append(u)
    # 找起点
    if directed:
        indeg = [0] * n
        for u in range(n):
            for v in g[u]:
                indeg[v] += 1
        outdeg = [len(g[u]) for u in range(n)]
        for u in range(n):
            if outdeg[u] - indeg[u] == 1:
                start = u
                break
        else:
            start = next((u for u in range(n) if outdeg[u]), 0)
    else:
        odd = [u for u in range(n) if len(g[u]) % 2]
        start = odd[0] if odd else next(u for u in range(n) if g[u])
    # Hierholzer
    g = [adj[:] for adj in g]  # 拷贝一份用于删边
    path = []
    def dfs(u):
        while g[u]:
            v = g[u].pop()
            if not directed:
                g[v].remove(u)
            dfs(v)
        path.append(u)
    dfs(start)
    return path[::-1]
```
## 二、树

1、huffman编码树：
WPL计算很简单，但建树不容易。示例如下：

```python
class Node:
    def __init__(self, weight, char=None):
        self.weight = weight
        self.char = char
        self.left = None
        self.right = None
    def __lt__(self, other):
        if self.weight == other.weight:
            return self.char < other.char
        return self.weight < other.weight
def huffman_encoding(char_freq):
    heap = [Node(freq, char) for char, freq in char_freq.items()]
    heapq.heapify(heap)
    while len(heap) > 1:
        left = heapq.heappop(heap)
        right = heapq.heappop(heap)
        merged = Node(left.weight + right.weight, min(left.char, right.char))
        merged.left = left
        merged.right = right
        heapq.heappush(heap, merged)
    return heap[0]
def external_path_length(node, depth=0):
    if node is None:
        return 0
    if node.left is None and node.right is None:
        return depth * node.weight
    return (external_path_length(node.left, depth + 1) +
            external_path_length(node.right, depth + 1))
```

2、前序与中序构建二叉树：
```python
class TreeNode:
    def __init__(self, val=0, left=None, right=None):
        self.val = val
        self.left = left
        self.right = right
def buildTree(preorder, inorder):
    # 用哈希表快速定位中序里的根节点位置
    inorder_map = {val: idx for idx, val in enumerate(inorder)}
    def helper(pre_left, pre_right, in_left, in_right):
        if pre_left > pre_right:
            return None
        # 前序第一个就是根
        root_val = preorder[pre_left]
        root = TreeNode(root_val)
        # 根在中序中的位置
        in_root = inorder_map[root_val]
        # 左子树节点个数
        left_size = in_root - in_left
        # 递归构建左右子树
        root.left = helper(pre_left + 1, pre_left + left_size, in_left, in_root - 1)
        root.right = helper(pre_left + left_size + 1, pre_right, in_root + 1, in_right)
        return root
    return helper(0, len(preorder) - 1, 0, len(inorder) - 1)
```

3、二叉堆：给出简单实现以供参考
```python
class MinHeap:
    def __init__(self, nums=None):
        self.heap = nums[:] if nums else []
        self._heapify()
    def _heapify(self):
        for i in range((len(self.heap) >> 1) - 1, -1, -1):
            self._sift_down(i)
    def _sift_down(self, i):
        n = len(self.heap)
        while True:
            smallest = i
            left = (i << 1) + 1
            right = left + 1
            if left < n and self.heap[left] < self.heap[smallest]:
                smallest = left
            if right < n and self.heap[right] < self.heap[smallest]:
                smallest = right
            if smallest == i:
                break
            self.heap[i], self.heap[smallest] = self.heap[smallest], self.heap[i]
            i = smallest
    def _sift_up(self, i):
        while i > 0:
            parent = (i - 1) >> 1
            if self.heap[i] < self.heap[parent]:
                self.heap[i], self.heap[parent] = self.heap[parent], self.heap[i]
                i = parent
            else:
                break
    def heappush(self, val):
        self.heap.append(val)
        self._sift_up(len(self.heap) - 1)
    def heappop(self):
        if not self.heap:
            return None
        self.heap[0], self.heap[-1] = self.heap[-1], self.heap[0]
        val = self.heap.pop()
        if self.heap:
            self._sift_down(0)
        return val
```

4、树状数组&非递归线段树模板：（视情况套不同模板）
```python
# ==================== 树状数组 ====================
# 用途：单点修改 + 区间求和
class BIT:
    def __init__(self, n):
        self.tree = [0] * (n + 1)
    def add(self, i, delta):
        while i < len(self.tree):
            self.tree[i] += delta
            i += i & -i
    def sum(self, i):
        s = 0
        while i > 0:
            s += self.tree[i]
            i -= i & -i
        return s
    def range_sum(self, l, r):
        return self.sum(r) - self.sum(l - 1)
# ==================== 非递归线段树 ====================
# 用途：单点修改 + 区间查询（求和/最值/gcd等可合并操作）
def build(arr, n):
    """构建线段树"""
    # 将叶⼦节点插⼊树的后半部分
    for i in range(n):
        tree[n + i] = arr[i]
    # ⾃底向上计算⽗节点的值
    for i in range(n - 1, 0, -1):
        # tree[i] = 左孩⼦ + 右孩⼦
        tree[i] = tree[i << 1] + tree[i << 1 | 1]
def update(p, value, n):
    """单点更新: 将索引 p 处的值改为 value"""
    p += n
    tree[p] = value
    # 向上回溯更新所有祖先节点
    i = p
    while i > 1:
        # tree[i>>1] 是⽗节点，tree[i] 和 tree[i^1] 是左右两个⼦节点
        tree[i >> 1] = tree[i] + tree[i ^ 1]
        i >>= 1
def query(l, r, n):
    """区间查询: 求 [l, r) 的和 (左闭右开)"""
    res = 0
    l += n
    r += n
    while l < r:
        # 如果 l 是右孩⼦ (奇数)，则它不在⽗节点的完整覆盖范围内
        if l & 1:
            res += tree[l]
            l += 1
        # 如果 r 是右孩⼦ (奇数)，则 r-1 是左孩⼦，将其计⼊结果
        if r & 1:
            r -= 1
            res += tree[r]
        # 移向上⼀层
        l >>= 1
        r >>= 1
    return res
```

5、前缀树Trie：
需注意每个节点需要有end_of_words值，也可以加上used_by_words值便于删除节点，否则也可以用回溯法删除。模板：
```python
class TrieNode:
    def __init__(self):
        self.child = {}
        self.cnt = 0
        self.end = 0
class Trie:
    def __init__(self):
        self.root = TrieNode()
    def insert(self, word):
        node = self.root
        for c in word:
            if c not in node.child:
                node.child[c] = TrieNode()
            node = node.child[c]
            node.cnt += 1
        node.end += 1
    def search(self, word):
        """略"""
    def startsWith(self, prefix):
        """略"""
    def delete(self, word):
        if not self.search(word):
            return
        node = self.root
        for c in word:
            next_node = node.child[c]
            next_node.cnt -= 1
            if next_node.cnt == 0:
                del node.child[c]
                return
            node = next_node
        node.end -= 1
```

## 三、位运算与集合常规操作

```python
a & b      # 与
a | b      # 或
a ^ b      # 异或
~a         # 取反
a << n     # 左移（乘 2^n）
a >> n     # 右移（除 2^n）
bin(x)                    # '0b101010' 
x.bit_length()            # 二进制位数（不含前导0）
x.bit_count()         
bin(x).count('1')
# 2. 最低位 1
lowbit = x & -x                    # 保留最低位1，其余变0
# 3. 消去最低位 1
x = x & (x - 1)                    # 去掉最低位的1
# 4. 获取第 k 位（0-based）
bit = (x >> k) & 1                 # 第k位是0还是1
# 5. 设置第 k 位为 1
x = x | (1 << k)
# 6. 设置第 k 位为 0
x = x & ~(1 << k)
# 7. 翻转第 k 位
x = x ^ (1 << k)
# 8. 判断奇偶
is_odd = x & 1                     # 1奇0偶
# 9. 交换两数
a ^= b
b ^= a
a ^= b
# 10. 判断是否为 2 的幂
is_power2 = x > 0 and (x & (x - 1)) == 0
# 11. 获取最低位 1 的位置（0-based）
pos = (x & -x).bit_length() - 1
# 12. 判断两数同号
same_sign = (a ^ b) >= 0
"""集合(均可原地修改：加等号)"""
a | b               # 并集
a & b               # 交集
a - b               # 差集（a 有 b 无）
a ^ b               # 对称差集（不相交的部分）
a in b
```

## 四、求逆序数

法一：BIT+离散化技巧，BIT存出现次数。
法二：归并排序，模板如下：

```python
def merge_sort(nums):
    def merge(l, r):
        if l >= r:
            return 0
        mid = (l + r) >> 1
        cnt = merge(l, mid) + merge(mid + 1, r)
        i, j = l, mid + 1
        tmp = []
        while i <= mid and j <= r:
            if nums[i] <= nums[j]:
                tmp.append(nums[i])
                i += 1
            else:
                tmp.append(nums[j])
                j += 1
                cnt += mid - i + 1  # nums[i..mid] 都大于 nums[j]
        tmp.extend(nums[i:mid + 1])
        tmp.extend(nums[j:r + 1])
        nums[l:r + 1] = tmp
        return cnt
    return merge(0, len(nums) - 1)
```

## 五、调度场算法（中缀表达式转后缀）

核心思想：数字直接输出，运算符进栈，按优先级决定何时弹出。

```python
def infix_to_postfix(tokens):
    # 优先级表
    priority = {'+': 1, '-': 1, '*': 2, '/': 2, '^': 3}
    output = []
    stack = []
    for token in tokens:
        # 1. 数字直接输出
        if token.isdigit() or token.isalpha():
            output.append(token)
        # 2. 左括号直接入栈
        elif token == '(':
            stack.append(token)
        # 3. 右括号：弹出直到左括号
        elif token == ')':
            while stack and stack[-1] != '(':
                output.append(stack.pop())
            stack.pop()  # 丢掉 '('
        # 4. 运算符：弹出优先级 >= 当前的全部，再入栈
        else:
            while stack and stack[-1] != '(' and priority.get(stack[-1], 0) >= priority.get(token, 0):
                output.append(stack.pop())
            stack.append(token)
    # 5. 剩余全部弹出
    while stack:
        output.append(stack.pop())
    return output
```

## 六、链表技巧

```python
class ListNode:
    def __init__(self, val=0, next=None):
        self.val = val
        self.next = next
"""链表反转"""
def reverse_linked_list(head: ListNode) -> ListNode:
    prev = None
    curr = head
    while curr is not None:
        next_node = curr.next  # 暂存当前节点的下⼀个节点
        curr.next = prev       # 将当前节点的下⼀个节点指向前⼀个节点
        prev = curr            # 前⼀个节点变为当前节点
        curr = next_node       # 当前节点变更为原先的下⼀个节点
    return prev
"""查找中间节点（快慢指针）"""
def find_middle_node(head):
    slow = fast = head
    while fast and fast.next:
        slow = slow.next
        fast = fast.next.next
    return slow
```
补充：判环及环的入口：用快慢指针，相遇则有环。相遇后慢指针回到入口，快指针此后一次只走一步，交点就是入口。

