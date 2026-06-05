http://cs101.openjudge.cn/
这是数算B期末机考的cheating sheet。约80页，4合1双面打印刚好10张A4纸张。内容主要是灵茶山艾府写的一些模板[分享｜如何科学刷题？ - 讨论 - 力扣（LeetCode）](https://leetcode.cn/discuss/post/3141566/ru-he-ke-xue-shua-ti-by-endlesscheng-q3yd/)，我做题后与ai一起修改得到的模板，闫老师课件上一些题目的代码。还有少部分内容是来自其它同学在群里分享的cheating sheet的代码。制作目的主要是考试时忘了可以查找一下，并且是在刷题过程中随时添加的，知识点的排序并没有一个明确的逻辑，大致是按使用程度和重要性排序的。
供学习参考使用。
## 拓扑排序
```python
# 返回有向无环图（DAG）的其中一个拓扑序
# 如果图中有环，返回空列表
# 节点编号从 0 到 n-1
def topologicalSort(n: int, edges: List[List[int]]) -> List[int]:
    g = [[] for _ in range(n)]
    in_deg = [0] * n
    for x, y in edges:
        g[x].append(y)
        in_deg[y] += 1  # 统计 y 的先修课数量
    topo_order = []
    q = deque(i for i, d in enumerate(in_deg) if d == 0)  # 没有先修课，可以直接上
    while q:
        x = q.popleft()
        topo_order.append(x)
        for y in g[x]:
            in_deg[y] -= 1  # 修完 x 后，y 的先修课数量减一
            if in_deg[y] == 0:  # y 的先修课全部上完
                q.append(y)  # 加入学习队列
    if len(topo_order) < n:  # 图中有环
        return []
    return topo_order
```
## 关键路径
```python
from collections import deque  
def solve():  
    n, m = map(int, input().split())  
    graph = [[] for _ in range(n)]  
    edges = []  
    indeg = [0] * n  
    outdeg = [0] * n  
    for _ in range(m):  
        u, v, w = map(int, input().split())  
        edges.append((u, v, w))  
        graph[u].append((v, w))  
        indeg[v] += 1  
        outdeg[u] += 1  
    # 拓扑排序  
    q = deque()  
    topo = []  
    indeg_copy = indeg.copy()  
    for i in range(n):  
        if indeg_copy[i] == 0:  
            q.append(i)  
    while q:  
        u = q.popleft()  
        topo.append(u)  
        for v, w in graph[u]:  
            indeg_copy[v] -= 1  
            if indeg_copy[v] == 0:  
                q.append(v)  
    # 判断是否有环  
    if len(topo) != n:  
        print("No")  
        return  
    # 计算 ve（最早发生时间）  
    ve = [0] * n  
    for u in topo:  
        for v, w in graph[u]:  
            if ve[u] + w > ve[v]:  
                ve[v] = ve[u] + w  
    # 计算 vl（最晚发生时间）  
    vl = [float('inf')] * n    #[max(ve)]*n
    vl[topo[-1]] = ve[topo[-1]]  
    for u in reversed(topo):  
        for v, w in graph[u]:  
            if vl[v] - w < vl[u]:  
                vl[u] = vl[v] - w  
    # 标记关键活动  
    critical = [[False] * n for _ in range(n)]  
    for u, v, w in edges:  
        if ve[u] + w == vl[v]:  
            critical[u][v] = True  
    # 找出所有起点（入度为0）和终点（出度为0）  
    starts = [i for i in range(n) if indeg[i] == 0]  
    ends = set(i for i in range(n) if outdeg[i] == 0)  
    # DFS 找所有关键路径  
    all_paths = []  
    def dfs(current, path):  
        if current in ends:  
            all_paths.append(path.copy())  
            return  
        # 按终点编号排序以保证字典序  
        next_nodes = sorted([v for v, _ in graph[current] if critical[current][v]])  
        for v in next_nodes:  
            path.append(v)  
            dfs(v, path)  
            path.pop()  
    for start in starts:  
        dfs(start, [start])  
    # 路径已经是字典序（因为 DFS 时按顺序遍历）  
    print("Yes")  
    for path in all_paths:  
        print("->".join(map(str, path)))  
if __name__ == "__main__":  
    solve()
```
## dijkstra
```python
# 返回从起点 start 到每个点的最短路长度 dis，如果节点 x 不可达，则 dis[x] = math.inf
# 要求：没有负数边权。 有向图或无向图均可处理，无向图可视为两条方向相反的有向边。可以处理环图
# 时间复杂度 O(n + mlogm)，其中 m 是 edges 的长度。注意堆中有 O(m) 个元素
def shortestPathDijkstra(n: int, edges: List[List[int]], start: int) -> List[int]:
    # 注：如果节点编号从 1 开始（而不是从 0 开始），可以把 n 加一
    g = [[] for _ in range(n)]  # 邻接表
    for x, y, wt in edges:
        g[x].append((y, wt))
        # g[y].append((x, wt))  # 无向图加上这行
    #prev = [-1] * n
    dis = [inf] * n
    dis[start] = 0  # 起点到自己的距离是 0
    h = [(0, start)]  # 堆中保存 (起点到节点 x 的最短路长度，节点 x)
    while h:
        dis_x, x = heappop(h)
        if dis_x > dis[x]:  # x 之前出堆过
            continue
        for y, wt in g[x]:
            new_dis_y = dis_x + wt
            if new_dis_y < dis[y]:
	            #prev[y] = x
                dis[y] = new_dis_y  # 更新 x 的邻居的最短路
                # 懒更新堆：只插入数据，不更新堆中数据
                # 相同节点可能有多个不同的 new_dis_y，除了最小的 new_dis_y，其余值都会触发上面的 continue
                heappush(h, (new_dis_y, y))
    return dis

def reconstruct_path(prev, start, target):#"""从prev数组中重建路径"""
    path = []
    curr = target
    while curr != -1:
        path.append(curr)
        curr = prev[curr]
    path.reverse()
```
同余最短路。找a,b,c中最小的一个非0数当作模，然后找每个余数对应的可到达的最小值，在最小值之上，加上这个模的整数倍就一定能到达了。找每个余数对应的最小值就用dijkstra，每个余数看作一个节点，单向边的边权就是a,b,c中除开模的其它数。注意不用显式建边。
## Floyd

```python
# 返回一个二维列表，其中 (i,j) 这一项表示从 i 到 j 的最短路长度
# 如果无法从 i 到 j，则最短路长度为 math.inf
# 允许负数边权
# 如果计算完毕后，存在 i，使得从 i 到 i 的最短路长度小于 0，说明图中有负环
# 节点编号从 0 到 n-1
# 时间复杂度 O(n^3 + m)，其中 m 是 edges 的长度
def shortestPathFloyd(self, n: int, edges: List[List[int]]) -> List[List[int]]:
    f = [[inf] * n for _ in range(n)]
    for i in range(n):
        f[i][i] = 0
    for x, y, wt in edges:
        f[x][y] = min(f[x][y], wt)  # 如果有重边，取边权最小值
        f[y][x] = min(f[y][x], wt)  # 无向图
    for k in range(n):
        for i in range(n):
            if f[i][k] == inf:  # 针对稀疏图的优化
                continue
            for j in range(n):
                f[i][j] = min(f[i][j], f[i][k] + f[k][j])
    return f
```
Floyd可以检测负权环 。Floyd 运⾏结束后，只需检查对⻆线元素 dist\[i]\[i]，如果出现负数，则说明该顶点在某个负权环上。
Floyd-Warshall 算法完全⽀持负权边。这是 Floyd-Warshall 优于 Dijkstra 算法的⼀个重要特性。但它在使⽤负权图时有⼀个核⼼前提：图中不能包含“负权环”。（Negative Cycle）。
## Bellman Ford
```python
def bellman_ford(n, edges, start):  #n为顶点数
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
# 图是边列表，每条边是 (起点, 终点, 权重)
edges = [(0, 1, 5),(0, 2, 4),(1, 3, 3),(2, 1, 6),(3, 2, -2)]  V = 4  source = 0
print(bellman_ford(V, edges, source))
```
```python
def bellman_ford_with_super_source(n, edges):#图不连通的话上面算法有负权环却不一定能检测到，如果只是想检测整个图中有没有负权环，可以这么干
    INF = float('inf')"""添加虚拟源点连接到所有顶点"""
    dist = [0] * n  # 关键：所有点初始为0
    for i in range(n - 1):# 添加虚拟源点的效果等价于：dist[0..n-1] = 0
        updated = False
        for u, v, w in edges:# 不需要真的加边
            if dist[u] + w < dist[v]:# 正常进行 V-1 次松弛
                dist[v] = dist[u] + w
                updated = True
        if not updated:
            break
    for u, v, w in edges:# 检测负环
        if dist[u] + w < dist[v]:
            return True
    return False
```
```python
def has_cycle_floyd(n, edges):#有向图检测环就把边权初始化为-1即可
    # 初始化距离矩阵
    INF = float('inf')
    dist = [[INF] * n for _ in range(n)]
    # 自己到自己的距离为0
    for i in range(n):
        dist[i][i] = 0
    # 设置边的权重为-1（表示反向边），这样环的累计距离会变成负数
    for u, v in edges:
        dist[u][v] = -1
    # Floyd-Warshall算法
    for k in range(n):
        for i in range(n):
            for j in range(n):
                if dist[i][k] < INF and dist[k][j] < INF:
                    if dist[i][k] + dist[k][j] < dist[i][j]:
                        dist[i][j] = dist[i][k] + dist[k][j]
    # 检查是否存在环：如果dist[i][i] < 0，说明有负环（有向环）
    for i in range(n):
        if dist[i][i] < 0:
            return True
    return False
```

```python
class Solution:  #Bellman Ford   最多(k+1)条边到终点时，就循环(k+1)次即可。
    def findCheapestPrice(self, n: int, flights: List[List[int]], src: int, dst: int, k: int) -> int:
        dist = [float('inf')] * n
        dist[src] = 0
        for _ in range(k+1):
            prev=dist.copy()  #注意是要用上一轮的更新
            for u, v, w in flights:
                if prev[u] != float('inf') and prev[u] + w < dist[v]:
                    dist[v] = prev[u] + w
        ans=dist[dst]
        return ans if ans!=float('inf') else -1
```
spfa
```python 
from collections import deque
def spfa_optimized(graph, V, source):
    dist = [float('inf')] * V
    dist[source] = 0
    queue = deque([source])
    in_queue = [False] * V
    in_queue[source] = True
    # 记录每个点的入队次数
    count = [0] * V
    count[source] = 1
    while queue:
        # SLF 优化: 如果队首元素距离太大，考虑从后面找个更小的(这里简单实现popleft)
        u = queue.popleft()
        in_queue[u] = False
        for v, w in graph[u]:
            if dist[u] + w < dist[v]:
                dist[v] = dist[u] + w
                if not in_queue[v]:
                    count[v] += 1
                    if count[v] >= V:  # 判定负环（不行试试V+1）
                        return "Error: Negative Cycle Detected"
                    # SLF (Small Label First) 优化
                    if queue and dist[v] < dist[queue[0]]:
                        queue.appendleft(v)
                    else:
                        queue.append(v)
                    in_queue[v] = True
    return dist
# 原始的边列表
edges = [(0, 1, 5),(0, 2, 4),(1, 3, 3),(2, 1, 6),(3, 2, -2)]   V = 4  source = 0
# 将边列表转换为邻接表
graph = [[] for _ in range(V)]
for u, v, w in edges:
    graph[u].append((v, w))
# 调用 SPFA 算法
result = spfa_optimized(graph, V, source)
```

## SCC
```python
def kosaraju(n, adj):
    visited = [False] * n
    stack = []
    # 1. 第一次 DFS：获取完成时间压入栈
    def dfs1(u):
        visited[u] = True
        for v in adj[u]:
            if not visited[v]: dfs1(v)
        stack.append(u)
    # 2. 生成转置图
    adj_rev = [[] for _ in range(n)]
    for u in range(n):
        for v in adj[u]:
            adj_rev[v].append(u)
    for i in range(n):
        if not visited[i]: dfs1(i)
    # 3. 第二次 DFS：按栈逆序在转置图上搜索
    visited = [False] * n
    sccs = []
    while stack:
        u = stack.pop()
        if not visited[u]:
            component = []
            def dfs2(curr):
                visited[curr] = True
                component.append(curr)
                for v in adj_rev[curr]:
                    if not visited[v]: dfs2(v)
            dfs2(u)
            sccs.append(component)
    return sccs
```

```python
import sys
sys.setrecursionlimit(200000)
# 读取输入
n, m = map(int, sys.stdin.readline().split())
graph = [[] for _ in range(n + 1)]
for _ in range(m):
	u, v = map(int, sys.stdin.readline().split())
	graph[u].append(v)
# Tarjan算法
dfn = [0] * (n + 1)  # 访问次序（时间戳）
low = [0] * (n + 1)  # 能追溯到的栈中的最早节点
stack = []
in_stack = [False] * (n + 1)
timestamp = 0
# 记录每个节点所属的强连通分量
scc_id = [0] * (n + 1)
scc_count = 0
sccs = []  # 存储每个强连通分量的节点列表
def tarjan(u):
	global timestamp, scc_count
	timestamp += 1
	dfn[u] = low[u] = timestamp
	stack.append(u)
	in_stack[u] = True
	for v in graph[u]:
		if dfn[v] == 0:  # 未访问
			tarjan(v)
			low[u] = min(low[u], low[v])
		elif in_stack[v]:  # 在栈中
			low[u] = min(low[u], dfn[v])
	# 如果u是强连通分量的根节点
	if low[u] == dfn[u]:
		scc_count += 1
		component = []
		while True:
			w = stack.pop()
			in_stack[w] = False
			scc_id[w] = scc_count
			component.append(w)
			if w == u:
				break
		component.sort()  # 按节点编号大小排序
		sccs.append(component)
# 对每个未访问的节点执行Tarjan算法
for i in range(1, n + 1):
	if dfn[i] == 0:
		tarjan(i)
# 输出结果
# 按照节点顺序输出每个节点所属的强连通分量
sccs.sort(key=lambda x:x[0])  
print(scc_count,end='\n')  
sys.stdout.write('\n'.join([' '.join(map(str,x)) for x in sccs]))

#以下是缩点代码，vals为每个节点的权值，要求一条路径，使路径上权值和最大。
scc_sum=[sum(vals[u] for u in x)for x in sccs]
dag=[[] for _ in range(scc_count)]  #注意这里scc标号和节点编号都从0开始
edge_set=set()
for u in range(n):
    for v in g[u]:
        su,sv=scc_id[u],scc_id[v]
        if su!=sv and (su,sv) not in edge_set:
            edge_set.add((su,sv))
            dag[su].append(sv)
dp=scc_sum.copy()  #dp[u]记录以第u个scc为终点的最大路径权值和
for u in range(scc_count-1,-1,-1):#tarjan完是逆拓扑序
    for v in dag[u]:
        if dp[u]+scc_sum[v]>dp[v]:#scc_sum[v]是第v个scc的权值。
            dp[v]=dp[u]+scc_sum[v]
print(max(dp))

#间谍控制。缩点后，只要控制了入度为0的scc，就能控制整个图。
dag=[[] for _ in range(scc_count)]  
in_deg=[0]*scc_count  
for u in range(n):  
    for v in g[u]:  
        su,sv=scc_id[u],scc_id[v]  
        if su!=sv:  
            in_deg[sv]+=1  
control=True  
total=0  
min_node=float('inf')  
for su in range(scc_count):  
    if in_deg[su]!=0:   continue  
    val=min_vals[su]  
    if val!=float('inf'):  
        total+=val  
    else:  
        control=False  
#min_node可能在入度不为0的scc中？对  
#还要单独计算不可空最小节点  
if not control:  
    controls=[False]*n  
    for su,val in enumerate(min_vals):  
        if val != float('inf'):  
            controls[su]=True  
    q=deque([su for su,val in enumerate(min_vals) if val!=float('inf')])  
    while q:  
        su=q.popleft()  
        for sv in dag[u]:  
            if not controls[sv]:  
                controls[sv]=True  
                q.append(sv)  
    for su,ok in enumerate(controls):  
        if not ok:  
            min_node=min(min_node,min_nodes[su])  
if control:  
    print(f'YES\n{total}')  
else:  
    print(f'NO\n{min_node+1}')   #编号从1开始
```
popular cows：- 在有向图中，如果某头牛被所有其他牛认为是 popular，那么这个牛所在的**强连通分量（SCC）**中，所有牛都能互相认为对方 popular。- 从任意一头牛出发，都能到达这头牛，说明这个 SCC 应该是**出度为 0** 的 SCC-。 如果存在一个出度为 0 的 SCC，则该 SCC 中的牛都能被所有其他牛到达。- 如果存在多个出度为 0 的 SCC，则答案为 0（因为无法互相到达）
Network of Schools算scc部分与上题一样。对于taskA，只要所有入度为0的节点拿到了软件，就都能拿到。对于taskB，在 SCC 缩点后的 DAG 中，若入度为 0 的 SCC 个数是 `in_zero`，出度为 0 的 SCC 个数是 `out_zero`，那么需要添加的边数是 `max(in_zero, out_zero)`。但如果整个图已经是一个 SCC（缩点后只有一个点），则答案为 0。
```python
# 计算缩点后的入度和出度
    in_degree = [0] * scc_count
    out_degree = [0] * scc_count
    for u in range(n):
        for v in graph[u]:
            if scc_id[u] != scc_id[v]:
                out_degree[scc_id[u]] += 1
                in_degree[scc_id[v]] += 1
    in_zero = sum(1 for d in in_degree if d == 0)
    out_zero = sum(1 for d in out_degree if d == 0)
    if scc_count == 1:
        print(1)   # Subtask A
        print(0)   # Subtask B
    else:
        print(in_zero)               # Subtask A
        print(max(in_zero, out_zero))# Subtask B
```

tarjan求割点。在无向连通图中，如果删除某个顶点及其关联的所有边后，图的连通分量数量增加，那么这个顶点称为**割点**。
```python
def find_cut_points(graph, n):  #找无向图的割点
    dfn = [-1] * n
    low = [-1] * n
    parent = [-1] * n
    is_cut = [False] * n
    time = 0
    def dfs(u):
        nonlocal time
        dfn[u] = low[u] = time
        time += 1
        child_count = 0
        for v in graph[u]:
            if dfn[v] == -1:  # 树边
                parent[v] = u
                child_count += 1
                dfs(v)
                low[u] = min(low[u], low[v])
                # 割点判断
                if parent[u] == -1 and child_count > 1:  # 根节点
                    is_cut[u] = True
                if parent[u] != -1 and low[v] >= dfn[u]:  # 非根节点
                    is_cut[u] = True
            elif v != parent[u]:  # 回边
                low[u] = min(low[u], dfn[v])
    for i in range(n):
        if dfn[i] == -1:
            dfs(i)
    return is_cut
```
tarjan求桥。在无向连通图中，如果删除某条边后，图的连通分量数量增加，那么这条边称为**桥**（或割边）。
```python
import sys  
sys.setrecursionlimit(1000000)
def find_bridges(n, m, edges):
    # 构建邻接表
    graph = [[] for _ in range(n + 1)]
    for a, b in edges:
        graph[a].append(b)
        graph[b].append(a)
    # 初始化
    dfn = [0] * (n + 1)  # 访问时间戳
    low = [0] * (n + 1)  # 最早能回溯到的祖先
    visited = [False] * (n + 1)
    bridges = []
    timestamp = 0
    def tarjan(u, parent):
        nonlocal timestamp
        timestamp += 1
        dfn[u] = low[u] = timestamp
        visited[u] = True
        for v in graph[u]:
            if v == parent:  # 跳过父节点
                continue
            if not visited[v]:
                tarjan(v, u)
                low[u] = min(low[u], low[v])
                # 判断是否为桥
                # 如果子节点v的low值大于父节点u的dfn值，则边u-v是桥
                if low[v] > dfn[u]:
                    if u < v:# 确保a<b
                        bridges.append((u, v))
                    else:
                        bridges.append((v, u))
            else:
                # 遇到已经访问过的节点（不是父节点），更新low值
                low[u] = min(low[u], dfn[v])
    for i in range(1, n + 1):# 图可能不连通，但题目保证连通，这里还是处理一下
        if not visited[i]:
            tarjan(i, -1)
    bridges.sort()# 排序
    return bridges
def main():
    data = sys.stdin.read().strip().split()# 读取输入
    if not data:
        return
    n, m = int(data[0]), int(data[1])
    edges = []
    idx = 2
    for _ in range(m):
        a, b = int(data[idx]), int(data[idx + 1])
        edges.append((a, b))
        idx += 2
    bridges = find_bridges(n, m, edges)# 求桥
    for a, b in bridges:# 输出
        print(a, b)
if __name__ == "__main__":
    main()
```

```python
from typing import List
from collections import defaultdict
class Solution:
    def minimumCost(self, cost: List[int], roads: List[List[int]]) -> int:
        n = len(cost)
        adj = defaultdict(list)
        for u, v in roads:
            adj[u].append(v)
            adj[v].append(u)
        dfn = [0] * n
        low = [0] * n
        tim = [0]
        cut = [False] * n  # 改成列表，方便判断
        vdcc = []
        st = []
        def tarjan(u, parent):
            tim[0] += 1
            dfn[u] = low[u] = tim[0]
            st.append(u)
            child = 0
            for v in adj[u]:
                if not dfn[v]:  # 未访问
                    child += 1
                    tarjan(v, u)
                    low[u] = min(low[u], low[v])
                    # 割点判断
                    if (parent == -1 and child > 1) or (parent != -1 and low[v] >= dfn[u]):
                        cut[u] = True
                        # 弹出vcc
                        vdcc.append([])
                        while True:
                            x = st.pop()
                            vdcc[-1].append(x)
                            if x == v:
                                break
                        vdcc[-1].append(u)  # 把u也加进去
                elif v != parent:  # 回边
                    low[u] = min(low[u], dfn[v])
            # 根节点的特殊情况处理
            if parent == -1 and not adj[u]:
                vdcc.append([u])
        # 遍历所有节点（图可能不连通，但题目保证连通）
        for i in range(n):
            if not dfn[i]:
                tarjan(i, -1)
                if st:  # 最后剩余的节点
                    vdcc.append(st[:])
                    st.clear()
        # 统计每个vdcc的信息
        cnt = [0] * len(vdcc)  # 割点数量
        minCost = [float('inf')] * len(vdcc)  # 非割点的最小cos
        for i, comp in enumerate(vdcc):
            for x in comp:
                if cut[x]:
                    cnt[i] += 1
                else:
                    minCost[i] = min(minCost[i], cost[x])
        # 特殊情况：只有一个vdcc（无割点）
        if len(vdcc) == 1:
            return min(cost)
        # 累加所有"叶子分量"的最小cost
        ans = 0
        maxMinCost = 0
        for i in range(len(vdcc)):
            if cnt[i] <= 1:  # 叶子分量或孤立边
                ans += minCost[i]
                maxMinCost = max(maxMinCost, minCost[i])
        return ans - maxMinCost
```
tarjan+树上背包
```python
import sys
sys.setrecursionlimit(500)
def solve():
    input = sys.stdin.read
    data = input().split()  idx = 0
    N = int(data[idx]); idx += 1
    M = int(data[idx]); idx += 1
    W = [0] * (N + 1)
    V = [0] * (N + 1)
    D = [0] * (N + 1)
    for i in range(1, N + 1):
        W[i] = int(data[idx]); idx += 1
    for i in range(1, N + 1):
        V[i] = int(data[idx]); idx += 1
    for i in range(1, N + 1):
        D[i] = int(data[idx]); idx += 1
    g = [[] for _ in range(N + 1)]
    for i in range(1, N + 1):
        if D[i] != 0:
            g[D[i]].append(i)
    # Tarjan
    dfn = [0] * (N + 1)
    low = [0] * (N + 1)
    instack = [False] * (N + 1)
    stack = []
    time = 0
    scc_id = [0] * (N + 1)
    scc_cnt = 0
    scc_w = []
    scc_v = []
    def tarjan(u):
        nonlocal time, scc_cnt
        time += 1
        dfn[u] = low[u] = time
        stack.append(u)
        instack[u] = True
        for v in g[u]:
            if dfn[v] == 0:
                tarjan(v)
                low[u] = min(low[u], low[v])
            elif instack[v]:
                low[u] = min(low[u], dfn[v])
        if dfn[u] == low[u]:
            scc_cnt += 1
            sw = 0
            sv = 0
            while True:
                x = stack.pop()
                instack[x] = False
                scc_id[x] = scc_cnt
                sw += W[x]
                sv += V[x]
                if x == u:
                    break
            scc_w.append(sw)
            scc_v.append(sv)
    for i in range(1, N + 1):
        if dfn[i] == 0:
            tarjan(i)
    new_g = [[] for _ in range(scc_cnt + 1)]
    has_parent = [False] * (scc_cnt + 1)
    for i in range(1, N + 1):
        u = scc_id[i]
        if D[i] != 0:
            v = scc_id[D[i]]
            if u != v:
                new_g[v].append(u)
                has_parent[u] = True
    for i in range(1, scc_cnt + 1):
        if not has_parent[i]:
            new_g[0].append(i)
    scc_w.insert(0, 0)
    scc_v.insert(0, 0)
    dp = [[0] * (M + 1) for _ in range(scc_cnt + 1)]
    size = [0] * (scc_cnt + 1)  # 子树容量限制
    def dfs(u):
        # 先强制选 u
        if u != 0:
            for j in range(M, scc_w[u] - 1, -1):
                dp[u][j] = scc_v[u]
            size[u] = scc_w[u]
        else:
            size[u] = 0
        for v in new_g[u]:
            dfs(v)
            # 合并子节点 v
            new_size = min(M, size[u] + size[v])
            for j in range(new_size, -1, -1):
                for k in range(0, min(j, size[v]) + 1):
                    if u != 0 and j - k < scc_w[u]:
                        continue
                    dp[u][j] = max(dp[u][j], dp[u][j - k] + dp[v][k])
            if u != 0:
                size[u] = min(M, size[u] + size[v])
            else:
                size[u] = min(M, size[u] + size[v])
    dfs(0)
    print(dp[0][M])
if __name__ == "__main__":
    solve()
```
## 位运算
```python
01字符串转化为对应的数值 s='1011'   num=int(s,2)  (结果为11)
二进制的反码表示是将每个 `1` 改为 `0` 且每个 `0` 变为 `1`。例如，二进制数101的二进制反码为010。
x左移n位  x<<n(相当于x*（2^n）)  右移：x>>n(相当于x//(2^n))
&按位与，|按位或，^按位异或。
n = 123     print(n.bit_length())   一个整数的二进制位数 时间复杂度与二进制表示长度相关
num = 42   二进制 101010，有3个1    print(num.bit_count())  # 输出: 3
def is_power_of_two(n):
    return n > 0 and (n & (n - 1)) == 0   判断2的幂
a = 5   # 二进制: ...0101 (无限个0在前面)
result = ~a  # ...1010 (补码表示)
print(result)   # -6
理解：~x = -x - 1
print(~5)   # -6
print(~(-5))  # 4
如果只需要取反特定位数（如8位）
masked = ~5 & 0xFF  # 取反8位: 11111010 = 250
print(masked)  # 250
lowbit(x) = x & -x        x = 12  # 1100   print(x & -x)  # 4 (0100)
判断 2 的幂 x & (x-1) == 0 。
x & (x-1) 得到的结果为x去掉最低位的1
x = 42
# 基本转换（带前缀）
print(bin(x))   # 0b101010
print(oct(x))   # 0o52
print(hex(x))   # 0x2a
# 去掉前缀（只保留数字部分）
print(bin(x)[2:])   # 101010
print(oct(x)[2:])   # 52
print(hex(x)[2:])   # 2a
# 带前缀
print(int("0b101010", 2))   # 42
print(int("0o52", 8))       # 42
print(int("0x2a", 16))      # 42
# 不带前缀
print(int("101010", 2))     # 42
print(int("52", 8))         # 42
print(int("2a", 16))        # 42
print(int("222", 4))        # 42 (四进制也支持)
x = 42
print(f"{x:b}")   # 101010 (二进制)
print(f"{x:#b}")  # 0b101010 (带前缀)
print(f"{x:08b}") # 00101010 (8位补零)
print(f"{x:>8b}") #   101010 (右对齐，宽度8)
print(f"{x:<8b}") # 101010   (左对齐)
print(f"{x:08b}")         # 00101010
print(f"{x:04X}")         # 002A
```

```python
本文将扫清位运算的迷雾，在集合论与位运算之间建立一座桥梁。
在高中，我们学了集合论（set theory）的相关知识。例如，包含若干整数的集合 S={0,2,3}。在编程中，通常用哈希表（hash table）表示集合。例如 Java 中的 HashSet，C++ 中的 std::unordered_set。
在集合论中，有交集 ∩、并集 ∪、包含于 ⊆ 等等概念。如果编程实现「求两个哈希表的交集」，需要一个一个地遍历哈希表中的元素。那么，有没有效率更高的做法呢？
该二进制登场了。
集合可以用二进制表示，二进制从低到高第 i 位为 1 表示 i 在集合中，为 0 表示 i 不在集合中。例如集合 {0,2,3} 可以用二进制数 1101 (2)​表示；反过来，二进制数 1101 (2)就对应着集合 {0,2,3}。
正式地说，包含非负整数的集合 S 可以用如下方式「压缩」成一个数字：
f(S)= i∈S∑2^i
例如集合 {0,2,3} 可以压缩成 2 0+2 2+2 3=13，也就是二进制数 1101 (2)​。
利用位运算「并行计算」的特点，我们可以高效地做一些和集合有关的运算。按照常见的应用场景，可以分为以下四类：集合与集合 集合与元素 遍历集合 枚举集合
m0=0x55555555  m1=0x33333333  m2=0x0f0f0f0f  m3=0x00ff00ff  m4=0x0000ffff
def reverseBits(self, n: int) -> int:  #颠倒二进制位
	n= n>>1&m0 | (n&m0)<<1
	n= n>>2&m1 | (n&m1)<<2
	n= n>>4&m2 | (n&m2)<<4
	n= n>>8&m3 | (n&m3)<<8
	return n>>16&m4 | (n&m4)<<16
a=100000
def sortByBits(self, arr: List[int]) -> List[int]:
	def counting(num):
		return num.bit_count()+num/a
	arr.sort(key=counting)
	return arr
```
## 基础
**from functools import lru_cache**
@lru_cache(maxsize=None)
考试时一定要防着读取有多余行，用他给的数截断。
子数组，子串是连续的。子序列不要求连续。它们一般默认要求非空。
import bisect
value = 123.456789      print(f"{value:.2f}")  # 123.46    :.Nf保留N位小数
ratio = 0.2567     print(f"{ratio:.1%}")
```python
try:
    num = int(input("请输入数字: "))     
    print(f"你输入了: {num}")
except:
    print("输入无效！")
try:
    num = int(input("请输入数字: "))
    result = 10 / num
    print(f"结果是: {result}")
except ValueError:
    print("错误：请输入有效的数字")
except ZeroDivisionError:
    print("错误：除数不能为零")
```
```python
for item in iterable:
    if condition:# 循环体
        break
else:# 循环正常结束（没有遇到 break）时执行
    pass
```
```python
class Person:
    def __init__(self, name, age):
        self.name = name
        self.age = age
    def __str__(self):  #给用户看的，print() 或 str() 时调用
        return f"姓名：{self.name}，年龄：{self.age}岁"
    def __repr__(self):   #给开发者看的，交互式环境或 repr() 时调用"""
        return f"Person('{self.name}', {self.age})"
    def __add__(self, other):   #定义 + 运算符的行为  #__sub__减法 \eq等于 \mul乘法
        if isinstance(other, Vector):
            return Vector(self.x + other.x, self.y + other.y)
class Animal:
    def __init__(self, name):
        self.name = name
    def speak(self):
        print(f"{self.name} 在叫")
# 子类继承 Animal
class Dog(Animal):
    def speak(self):  # 重写父类方法
        print(f"{self.name} 汪汪叫")
    def wag_tail(self):  # 子类新增方法
        print(f"{self.name} 摇尾巴")
```
```python
def gcd(a, b):
    while b:
        a, b = b, a % b
    return a
def lcm(a, b):
    return abs(a * b) // gcd(a, b)
text = "hello world, hello python"
result = text.replace("hello", "hi")
print(result)  # hi world, hi python    str.replace(old, new, count)
#`count`（可选）：替换次数，默认替换所有
bisect.bisect_left(a, x, lo=0, hi=len(a))
bisect.bisect_right(a, x, lo=0, hi=len(a))
bisect.insort_left(a, x, lo=0, hi=len(a))
`lo`：起始索引（包含），默认 0
`hi`：结束索引（不包含），默认 `len(a)`
arr = [10, 20, 30, 40, 50]
# 比所有元素都小
pos = bisect.bisect_left(arr, 5)   # 返回 0
pos = bisect.bisect_right(arr, 5)  # 返回 0
pos = bisect.bisect_left(arr, 100)   # 返回 5 (列表长度)
pos = bisect.bisect_right(arr, 100)  # 返回 5
arr = [1, 2, 4, 4, 6, 8]
print(bisect.bisect_left(arr, 4))   # 2 (第一个4的位置)
print(bisect.bisect_right(arr, 4))  # 4 (最后一个4之后的位置)
```
只有一行时不要用read，会报错，用readline
在一个无向简单图中，如果任意两个不同的顶点之间都恰好有一条边相连，则称该图为完全图。   补图连通块 + 缩点生成树  0-w最小生成树
设a,b是整数,且 b≠0.如果存在一个整数 q，使得a=b×q那么我们就说 b 整除 a，记作 b∣a。
等价说法：a能被b整除，b是a的约数（因数），a除以b的余数为 0。
满⼆叉树（Full Binary Tree）：所有⾮叶⼦节点都有两个⼦节点。
完全⼆叉树（Complete Binary Tree）：只有最后⼀层可以不满，并且节点从左到右排
完全二叉树索引关系：左孩子2i+1,右孩子2i+2，父节点(i-1)//2  （根节点编号为0时）
二叉树的前序遍历：先访问节点值，再递归左右子树。中序遍历：递归左子树，访问节点值，再递归右子树。后序遍历：递归左子树，递归右子树，再访问节点值。
树中连接两点有且仅有一条路径，不然就形成了环。
在一棵有根树中，从根节点到某个节点 x的路径上，除 x 自身以外的所有节点，都称为 x 的祖先。
最近公共祖先（LCA）两个节点共同祖先中深度最大的那个
后代（descendant）向下走能到达的所有节点（不含自身）
树(二叉，多叉都是)中每个节点只有一个父节点（除了根）  （无论有多少孩子，父节点唯一）
求最大公约数math.gcd(a,b)  import math
sys.setrecursionlimit(10000)      re就看看是否爆栈   递归深度比树的高度大就行
result = set1 | set2    两个集合的并集。    set1 & set2   交集。 set1-set2  差集(取出在set1但不在set2的元素).
避免oj静态检查：#pylint:skip-file   整个程序最前面加上
列表原地反转：alist.reverse()
矩阵转置：transposed=list(map(list,zip(\*matrix)))
集合加减元素：aset.add(123)   aset.remove('b')(没有会报错)   aset.discard('b')(没有不会报错) 
aset.update(\['a',1,2])把列表中的元素逐个加到集合中
import copy 深拷贝  new_obj = copy.deepcopy(original_obj)
st1.sort(key=lambda x:x\[1],reverse=True)     排序
最外层函数 全局变量用global声明。   嵌套函数用nonlocal 声明。
import heapq     heapq.heapify(data)  # 原地转换为堆，时间复杂度O(n)   
min_value = heapq.heappop(heap)      heapq.heappush(heap, 3)   
result = heapq.heappushpop(heap, 2)  # 先推2，再弹最小
查看最小O(1)  heap\[0]
def  \__lt\__(self, other):   return self.priority < other.priority  # 数字越小优先级越高
def \__repr\__(self):   return f"\[P{self.priority}] {self.description}"
x.isdigit() 是 Python 中的字符串方法，用于判断字符串是否只包含数字字符。返回 True 或 False。    （isalpha()  看是否只含字母）
**大顶堆**（最大堆）：  每个节点的值 **都大于或等于** 它的子节点的值.
**小顶堆**（最小堆）：  每个节点的值 **都小于或等于** 它的子节点的值。
0-w最小生成树1.连接不用权重能连的边，然后把每个连接好的模块视为一个整体，对他们重新编号
2.对这些新的集合节点使用Kruskal算法建树
二叉搜索树：**左子树**上所有节点的值 **小于** 根节点的值，**右子树**上所有节点的值 **大于** 根节点的值。左、右子树本身也**都是二叉搜索树**。
```python
class NumMatrix:#二维前缀和 初始化时存下（0，0）到（i，j）矩形区域的元素和，然后调用时用几个矩形区域相减凑出结果。
    def __init__(self, matrix: List[List[int]]):
        m,n=len(matrix),len(matrix[0])
        pre=[[0]*(n+1) for _ in range(m+1)]
        for i in range(m):
            for j in range(n):
                pre[i+1][j+1]=matrix[i][j]+pre[i][j+1]+pre[i+1][j]-pre[i][j]
        self.pre=pre
    def sumRegion(self, row1: int, col1: int, row2: int, col2: int) -> int:
        return self.pre[row2+1][col2+1]+self.pre[row1][col1]-self.pre[row1][col2+1]-self.pre[row2+1][col1]
```
```python
from collections import OrderedDict  #lru缓存  get和put都能O（1）
class LRUCache:
    def __init__(self, capacity: int):
        self.cap=capacity
        self.od=OrderedDict()
    def get(self, key: int) -> int:
        if key not in self.od:
            return -1
        self.od.move_to_end(key,last=True)
        return self.od[key]
    def put(self, key: int, value: int) -> None:
        self.od[key]=value
        self.od.move_to_end(key,last=True)
        if len(self.od)>self.cap:
            self.od.popitem(last=False)
        return
```
3093.最长公共后缀查询  反转之后就能用前缀树模板了，不过节点定义中要加点东西
M1871.跳跃游戏 VII   前缀和优化dp，可以做到时空都是O(n)
1850H. The Third Letter  这题其实就是带权并查集合。
P1262\[POI 1996 R3] 间谍网络  tarjan缩点，缩点后，只要收买了所有入度为0的scc，整个图就都能被控制。不可控制时，不能被控制的最小节点编号可能在入度不为0的scc中，所以还要bfs一次看哪些scc不能被控制。
427C. Checkposts  tarjan缩点，每个scc都要有人看守，不只是入度为0的scc。
29702:二叉的水管  拓扑排序。建立大小顺序到这题完全二叉树编号的映射这个方法学会了
## 输入输出

```python
import sys
sys.stdout.write('\n'.join(map(str, results)) + '\n')
```
## 单调栈
```python
def dailyTemperatures(self, temperatures: List[int]) -> List[int]:
	n = len(temperatures) #栈中记录还没算出下一个更大元素的那些数的下标。
	ans = [0] * n
	st = []  # todolist
	for i, t in enumerate(temperatures):
		while st and t > temperatures[st[-1]]:
			j = st.pop()
			ans[j] = i - j
		st.append(i)
	return ans
def finalPrices(self, prices: List[int]) -> List[int]:
	n=len(prices)  #商品折扣后的最终价格
	ans=prices.copy()
	st=[]
	for i,x in enumerate(prices):
		while st and x<=prices[st[-1]]:
			ans[st.pop()]-=x
		st.append(i)
	return ans
	import sys
from array import array  #完美交易窗口
import bisect
h=sys.stdin.buffer.read().split()
n=int(h[0])
h=array('I',map(int,h[1:]))
max_st=[]     #左边界下标够成的单调递减栈
buy_st=[]     #买入点下标够成的单调递增栈
ans=0
for j in range(n):
    cur=h[j]
    while max_st and cur>h[max_st[-1]]:
        max_st.pop()
    left_board=max_st[-1] if max_st else -1
    while buy_st and cur<=h[buy_st[-1]]:
        buy_st.pop()
    if buy_st:
        i=bisect.bisect_right(buy_st,left_board)
        if i<len(buy_st):
            buy=buy_st[i]
            ans=max(ans,j-buy+1)
    max_st.append(j)
    buy_st.append(j)
print(ans)
```
## 单调队列
```python
# 计算 nums 的每个长为 k 的窗口的最大值
# 时间复杂度 O(n)，其中 n 是 nums 的长度
def maxSlidingWindow(nums: List[int], k: int) -> List[int]:
    ans = [0] * (len(nums) - k + 1)  # 窗口个数
    q = deque()  # 双端队列
    for i, x in enumerate(nums):
        # 1. 右边入
        while q and nums[q[-1]] <= x:
            q.pop()  # 维护 q 的单调性
        q.append(i)  # 注意保存的是下标，这样下面可以判断队首是否离开窗口
        # 2. 左边出
        left = i - k + 1  # 窗口左端点
        if q[0] < left:  # 队首离开窗口
            q.popleft()
        # 3. 在窗口左端点处记录答案
        if left >= 0:
            # 由于队首到队尾单调递减，所以窗口最大值就在队首
            ans[left] = nums[q[0]]
    return ans
def longestSubarray(self, nums: List[int], limit: int) -> int:
	min_q = deque()
	max_q = deque()#返回最长连续子数组的长度，
	ans = left = 0#该子数组中的任意两个元素之间的绝对差必须小于或者等于 `limit`_。_
	for i, x in enumerate(nums):
		# 1. 右边入
		while min_q and x <= nums[min_q[-1]]:
			min_q.pop()
		min_q.append(i)
		while max_q and x >= nums[max_q[-1]]:
			max_q.pop()
		max_q.append(i)
		# 2. 左边出
		while nums[max_q[0]] - nums[min_q[0]] > limit:
			left += 1
			if min_q[0] < left:  # 队首不在窗口中
				min_q.popleft()
			if max_q[0] < left:  # 队首不在窗口中
				max_q.popleft()
		# 3. 更新答案
		ans = max(ans, i - left + 1)
	return ans
def shortestSubarray(self, nums: List[int], k: int) -> int:  
    # 计算前缀和，转化为最近的，比该数大至少k的数。  
    n = len(nums)  #和至少为k的最短子数组
    pre = [0] * (n + 1)  
    ans = float('inf')  
    for i in range(n):  
        pre[i + 1] = pre[i] + nums[i]  
    q = deque([0])  
    for i in range(1, n + 1):  
        x = pre[i]  
        # 1  
        while q and x <= pre[q[-1]]:  
            q.pop()  
        q.append(i)  
        while x - pre[q[0]] >= k:  
            ans = min(ans, i - q[0])  # 注意前缀和的定义  
            q.popleft()  
    return ans if ans != float('inf') else -1
```
## 树状数组
```python
class FenwickTree:#注意下标
    def __init__(self, n: int):
        self.tree = [0] * (n + 1)  # 使用下标 1 到 n
    def __init__(self, nums: List[int]):  #用数组初始化就这么写
        n = len(nums)
        tree = [0] * (n + 1)
        for i, x in enumerate(nums, 1):  # i 从 1 开始
            tree[i] += x
            nxt = i + (i & -i)  # 下一个关键区间的右端点
            if nxt <= n:
                tree[nxt] += tree[i]
        self.nums = nums
        self.tree = tree
    # a[i] 增加 val
    # 1 <= i <= n
    # 时间复杂度 O(log n)
    def update(self, i: int, val: int) -> None:
        t = self.tree
        while i < len(t):
            t[i] += val
            i += i & -i
    # 计算前缀和 a[1] + ... + a[i]
    # 1 <= i <= n
    # 时间复杂度 O(log n)
    def pre(self, i: int) -> int:
        t = self.tree
        res = 0
        while i > 0:
            res += t[i]
            i &= i - 1
        return res
    # 计算区间和 a[l] + ... + a[r]
    # 1 <= l <= r <= n
    # 时间复杂度 O(log n)
    def query(self, l: int, r: int) -> int:
        if r < l:
            return 0
        return self.pre(r) - self.pre(l - 1)
```
## 线段树

```python
# 线段树有两个下标，一个是线段树节点的下标，另一个是线段树维护的区间的下标
# 节点的下标：从 1 开始，如果你想改成从 0 开始，需要把左右儿子下标分别改成 node*2+1 和 node*2+2
# 区间的下标：从 0 开始
class SegmentTree:
    def __init__(self, arr, default=0):
        # 线段树维护一个长为 n 的数组（下标从 0 到 n-1）
        # arr 可以是 list 或者 int
        # 如果 arr 是 int，视作数组大小，默认值为 default
        if isinstance(arr, int):
            arr = [default] * arr
        n = len(arr)
        self._n = n
        self._tree = [0] * (2 << (n - 1).bit_length())  #或开[0]*4n
        self._build(arr, 1, 0, n - 1)
    # 合并两个 val
    def _merge_val(self, a: int, b: int) -> int:
        return max(a, b)  # **根据题目修改**
    # 合并左右儿子的 val 到当前节点的 val
    def _maintain(self, node: int) -> None:
        self._tree[node] = self._merge_val(self._tree[node * 2], self._tree[node * 2 + 1])
    # 用 a 初始化线段树
    # 时间复杂度 O(n)
    def _build(self, a: List[int], node: int, l: int, r: int) -> None:
        if l == r:  # 叶子
            self._tree[node] = a[l]  # 初始化叶节点的值
            return
        m = (l + r) // 2
        self._build(a, node * 2, l, m)  # 初始化左子树
        self._build(a, node * 2 + 1, m + 1, r)  # 初始化右子树
        self._maintain(node)
    def _update(self, node: int, l: int, r: int, i: int, val: int) -> None:
        if l == r:  # 叶子（到达目标）
            # 如果想直接替换的话，可以写 self._tree[node] = val
            self._tree[node] = self._merge_val(self._tree[node], val)
            return
        m = (l + r) // 2
        if i <= m:  # i 在左子树
            self._update(node * 2, l, m, i, val)
        else:  # i 在右子树
            self._update(node * 2 + 1, m + 1, r, i, val)
        self._maintain(node)
    def _query(self, node: int, l: int, r: int, ql: int, qr: int) -> int:
        if ql <= l and r <= qr:  # 当前子树完全在 [ql, qr] 内
            return self._tree[node]
        m = (l + r) // 2
        if qr <= m:  # [ql, qr] 在左子树
            return self._query(node * 2, l, m, ql, qr)
        if ql > m:  # [ql, qr] 在右子树
            return self._query(node * 2 + 1, m + 1, r, ql, qr)
        l_res = self._query(node * 2, l, m, ql, qr)
        r_res = self._query(node * 2 + 1, m + 1, r, ql, qr)
        return self._merge_val(l_res, r_res)
    # 返回 [L,n-1] 中第一个 > v 的值的下标
    # 如果不存在，返回 -1
	def find(self,o: int, l: int, r: int, L: int, v: int) -> int:
		if self._tree[o] <= v:  # 区间最大值 <= v
			return -1  # 没有 > v 的数
		if l == r:  # 找到了
			return l
		m = (l + r) // 2
		if L <= m and (pos := self.find(o * 2, l, m, L, v)) >= 0:  # 递归左子树
			return pos
		return self.find(o * 2 + 1, m + 1, r, L, v)  # 递归右子树
    # 更新 a[i] 为 _merge_val(a[i], val)
    # 时间复杂度 O(log n)
    def update(self, i: int, val: int) -> None:
        self._update(1, 0, self._n - 1, i, val)
    # 返回用 _merge_val 合并所有 a[i] 的计算结果，其中 i 在闭区间 [ql, qr] 中
    # 时间复杂度 O(log n)
    def query(self, ql: int, qr: int) -> int:
        return self._query(1, 0, self._n - 1, ql, qr)
    # 获取 a[i] 的值
    # 时间复杂度 O(log n)
    def get(self, i: int) -> int:
        return self._query(1, 0, self._n - 1, i, i)
```

```python
class Node:
    __slots__ = 'val', 'todo'
class LazySegmentTree:
    # 懒标记初始值
    _TODO_INIT = 0  # **根据题目修改**
    def __init__(self, arr, default=0):
        # 线段树维护一个长为 n 的数组（下标从 0 到 n-1）
        # arr 可以是 list 或者 int
        # 如果 arr 是 int，视作数组大小，默认值为 default
        if isinstance(arr, int):
            arr = [default] * arr
        n = len(arr)
        self._n = n
        self._tree = [Node() for _ in range(2 << (n - 1).bit_length())]
        self._build(arr, 1, 0, n - 1)
    # 合并两个 val
    def _merge_val(self, a: int, b: int) -> int:
        return a + b  # **根据题目修改**
    # 合并两个懒标记
    def _merge_todo(self, a: int, b: int) -> int:
        return a + b  # **根据题目修改**
    # 把懒标记作用到 node 子树（本例为区间加）
    def _apply(self, node: int, l: int, r: int, todo: int) -> None:
        cur = self._tree[node]
        # 计算 tree[node] 区间的整体变化
        cur.val += todo * (r - l + 1)  # **根据题目修改**
        cur.todo = self._merge_todo(todo, cur.todo)
    # 把当前节点的懒标记下传给左右儿子
    def _spread(self, node: int, l: int, r: int) -> None:
        todo = self._tree[node].todo
        if todo == self._TODO_INIT:  # 没有需要下传的信息
            return
        m = (l + r) // 2
        self._apply(node * 2, l, m, todo)
        self._apply(node * 2 + 1, m + 1, r, todo)
        self._tree[node].todo = self._TODO_INIT  # 下传完毕
    # 合并左右儿子的 val 到当前节点的 val
    def _maintain(self, node: int) -> None:
        self._tree[node].val = self._merge_val(self._tree[node * 2].val, self._tree[node * 2 + 1].val)
    # 用 a 初始化线段树
    # 时间复杂度 O(n)
    def _build(self, a: List[int], node: int, l: int, r: int) -> None:
        self._tree[node].todo = self._TODO_INIT
        if l == r:  # 叶子
            self._tree[node].val = a[l]  # 初始化叶节点的值
            return
        m = (l + r) // 2
        self._build(a, node * 2, l, m)  # 初始化左子树
        self._build(a, node * 2 + 1, m + 1, r)  # 初始化右子树
        self._maintain(node)
    def _update(self, node: int, l: int, r: int, ql: int, qr: int, f: int) -> None:
        if ql <= l and r <= qr:  # 当前子树完全在 [ql, qr] 内
            self._apply(node, l, r, f)
            return
        self._spread(node, l, r)
        m = (l + r) // 2
        if ql <= m:  # 更新左子树
            self._update(node * 2, l, m, ql, qr, f)
        if qr > m:  # 更新右子树
            self._update(node * 2 + 1, m + 1, r, ql, qr, f)
        self._maintain(node)
    def _query(self, node: int, l: int, r: int, ql: int, qr: int) -> int:
        if ql <= l and r <= qr:  # 当前子树完全在 [ql, qr] 内
            return self._tree[node].val
        self._spread(node, l, r)
        m = (l + r) // 2
        if qr <= m:  # [ql, qr] 在左子树
            return self._query(node * 2, l, m, ql, qr)
        if ql > m:  # [ql, qr] 在右子树
            return self._query(node * 2 + 1, m + 1, r, ql, qr)
        l_res = self._query(node * 2, l, m, ql, qr)
        r_res = self._query(node * 2 + 1, m + 1, r, ql, qr)
        return self._merge_val(l_res, r_res)
    # 用 f 更新 [ql, qr] 中的每个 a[i]
    # 0 <= ql <= qr <= n-1
    # 时间复杂度 O(log n)
    def update(self, ql: int, qr: int, f: int) -> None:
        self._update(1, 0, self._n - 1, ql, qr, f)
    # 返回用 _merge_val 合并所有 a[i] 的计算结果，其中 i 在闭区间 [ql, qr] 中
    # 0 <= ql <= qr <= n-1
    # 时间复杂度 O(log n)
    def query(self, ql: int, qr: int) -> int:
        return self._query(1, 0, self._n - 1, ql, qr)
```

```python
import sys  
data=sys.stdin.read().strip().split('\n')  
k,n=map(int,data[0].split())  
t=LazySegmentTree((1<<k)-1)  
size=1<<k  
in_time = [0] * size  
out_time = [0] * size  
time = 0  #数组下标从0开始  
#DFS 将树扁平化为区间 [in_time, out_time]def dfs(u):  
    global time  
    in_time[u] = time  
    time += 1  
    left = 2 * u  
    right = 2 * u + 1  
    if left < size:  
        dfs(left)  
    if right < size:  
        dfs(right)  
    out_time[u] = time - 1  
dfs(1)
```
```python
import sys  #力场叠加模拟
data=sys.stdin.read().strip().split('\n')  
n,m=map(int,data[0].split())  
t=LazySegmentTree(n)  
ans=[]  
for x in data[1:]:  
    x=x.split()  
    if len(x)==3:  
        op,ql,qr=x  
        ql,qr=int(ql)-1,int(qr)-1  
        ans.append(t.query(1,0,n-1,ql,qr))  
    else:  
        op,ql,qr,v=x  
        ql,qr,v=int(ql)-1,int(qr)-1,int(v)  
        t.update(1,0,n-1,ql,qr,v)  
print('\n'.join(map(str,ans)))
```

## 并查集
merge是把根节点接在一起，注意别写错！
```python
class UnionFind:
    def __init__(self, n: int):
        # 一开始有 n 个集合 {0}, {1}, ..., {n-1}
        # 集合 i 的代表元是自己，大小为 1
        self._fa = list(range(n))  # 代表元
        self._size = [1] * n  # 集合大小
        self.cc = n  # 连通块个数
    # 返回 x 所在集合的代表元
    # 同时做路径压缩，也就是把 x 所在集合中的所有元素的 fa 都改成代表元
    def find(self, x: int) -> int:
        fa = self._fa
        # 如果 fa[x] == x，则表示 x 是代表元
        if fa[x] != x:
            fa[x] = self.find(fa[x])  # fa 改成代表元
        return fa[x]
    # 判断 x 和 y 是否在同一个集合
    def is_same(self, x: int, y: int) -> bool:
        # 如果 x 的代表元和 y 的代表元相同，那么 x 和 y 就在同一个集合
        # 这就是代表元的作用：用来快速判断两个元素是否在同一个集合
        return self.find(x) == self.find(y)
    # 把 from 所在集合合并到 to 所在集合中
    # 返回是否合并成功
    def merge(self, from_: int, to: int) -> bool:
        x, y = self.find(from_), self.find(to)
        if x == y:  # from 和 to 在同一个集合，不做合并
            return False
        self._fa[x] = y  # 合并集合。修改后就可以认为 from 和 to 在同一个集合了
        self._size[y] += self._size[x]  # 更新集合大小（注意集合大小保存在代表元上）
        # 无需更新 _size[x]，因为我们不用 _size[x] 而是用 _size[find(x)] 获取集合大小，但 find(x) == y，我们不会再访问 _size[x]
        self.cc -= 1  # 成功合并，连通块个数减一
        return True
    # 返回 x 所在集合的大小
    def get_size(self, x: int) -> int:
        return self._size[self.find(x)]  # 集合大小保存在代表元上    
    
    def union(x, y):
	    rx, ry = find(x), find(y)
	    if rx == ry:
	        return
	    if size[rx] < size[ry]:#按大小合并  
	        parent[rx] = ry
	        size[ry] += size[rx]
	    else:
	        parent[ry] = rx
	        size[rx] += size[ry]
		# 按秩合并：矮树接高树  (rank初始化成0就行)   按大小按秩二选一即可
	    if rank[rx] < rank[ry]:
	        parent[rx] = ry
	    elif rank[rx] > rank[ry]:
	        parent[ry] = rx
	    else:
	        parent[ry] = rx
	        rank[rx] += 1
```
```python
for s,u,v in edges1:  #并查集合判环
        if uf1.union(u,v):
            ans=min(ans,s)
        else:
            return -1
```

```python
class UnionFind:  # 带权
    def __init__(self, n: int):
        # 一开始有 n 个集合 {0}, {1}, ..., {n-1}
        # 集合 i 的代表元是自己，自己到自己的距离是 0
        self.fa = list(range(n))  # 代表元
        self.dis = [0] * n  # dis[x] 表示 x 到（x 所在集合的）代表元的距离
    # 返回 x 所在集合的代表元
    # 同时做路径压缩
    def find(self, x: int) -> int:
        fa = self.fa
        if fa[x] != x:
            root = self.find(fa[x])
            self.dis[x] += self.dis[fa[x]]  # 递归更新 x 到其代表元的距离
            fa[x] = root
        return fa[x]
    # 判断 x 和 y 是否在同一个集合（同普通并查集）
    def same(self, x: int, y: int) -> bool:
        return self.find(x) == self.find(y)
    # 计算从 from 到 to 的相对距离
    # 调用时需保证 from 和 to 在同一个集合中，否则返回值无意义
    def get_relative_distance(self, from_: int, to: int) -> int:
        self.find(from_)
        self.find(to)
        # to-from = (x-from) - (x-to) = dis[from] - dis[to]
        return self.dis[from_] - self.dis[to]
    # 合并 from 和 to，新增信息 to - from = value
    # 其中 to 和 from 表示未知量，下文的 x 和 y 也表示未知量
    # 如果 from 和 to 不在同一个集合，返回 True，否则返回是否与已知信息矛盾
    def merge(self, from_: int, to: int, value: int) -> bool:
        x, y = self.find(from_), self.find(to)
        dis = self.dis
        if x == y:  # from 和 to 在同一个集合，不做合并
            # to-from = (x-from) - (x-to) = dis[from] - dis[to] = value
            return dis[from_] - dis[to] == value
        #    x --------- y
        #   /           /
        # from ------- to
        # 已知 x-from = dis[from] 和 y-to = dis[to]，现在合并 from 和 to，新增信息 to-from = value
        # 由于 y-from = (y-x) + (x-from) = (y-to) + (to-from)
        # 所以 y-x = (to-from) + (y-to) - (x-from) = value + dis[to] - dis[from]
        dis[x] = value + dis[to] - dis[from_]  # 计算 x 到其代表元 y 的距离
        self.fa[x] = y
        return True
```
带权并查集异或版
```python
class UnionFind:  
    def __init__(self, n: int):  
        self.fa = list(range(n))  # fa[x] 是 x 的代表元  
        self.dis = [0] * n  # dis[x] = 从 x 到 fa[x] 的路径异或和  
    def find(self, x: int) -> int:  
        fa = self.fa  
        if fa[x] != x:  
            root = self.find(fa[x])  
            self.dis[x] ^= self.dis[fa[x]]  
            fa[x] = root  
        return fa[x]  
    def merge(self, from_: int, to: int, value: int) -> bool:  
        x, y = self.find(from_), self.find(to)  
        dis = self.dis  
        if x == y:  
            return dis[from_] ^ dis[to] == value  
        dis[x] = value ^ dis[to] ^ dis[from_]  
        self.fa[x] = y  
        return True  
class Solution:  
    def numberOfEdgesAdded(self, n: int, edges: List[List[int]]) -> int:  
        uf = UnionFind(n)  
        ans = 0  
        for x, y, w in edges:  
            if uf.merge(x, y, w):  #虫子的生活那道题就是w=1
                ans += 1  
        return ans
```
```python
import sys  #猫猫搭积木   按秩合并
class UnionFind:
    def __init__(self,n):
        self.fa=list(range(n))
        self.cc=n
        self.members=[[i] for i in range(n)]    #组里面的元素都记录在根节点
    def find(self,x):
        while x!=self.fa[x]:
            x=self.fa[x]
        return x
    def merge(self,x,y):
        fa_x,fa_y=self.find(x),self.find(y)
        if fa_x==fa_y:
            return
        if len(self.members[fa_x])<len(self.members[fa_y]):
            fa_x,fa_y=fa_y,fa_x
        for y in self.members[fa_y]:
            self.fa[y]=fa_x
            self.members[fa_x].append(y)
        self.members[fa_y] = []
        self.cc-=1
        if len(self.members[fa_x])>=s:
            self.cc+=len(self.members[fa_x])-1
            for x in self.members[fa_x]:
                if x==fa_x: continue
                self.fa[x]=x
                self.members[x]=[x]
            self.members[fa_x]=[fa_x]
data=sys.stdin.read().strip().split('\n')
n,q,s=map(int,data[0].split())
uf=UnionFind(n)
ans=[]
for x in data[1:q+1]:
    u,v=map(int,x.split())
    u,v=u-1,v-1
    uf.merge(u,v)
    ans.append(uf.cc)
sys.stdout.write('\n'.join(map(str,ans)))
```
## 最小生成树
最小生成树是针对无向图的，在无向图中求一棵树,(n-1)条边，无环，连通所有点，且边权和最小。
```python
class UnionFind:
    def __init__(self, n: int):
        # 一开始有 n 个集合 {0}, {1}, ..., {n-1}
        # 集合 i 的代表元是自己
        self._fa = list(range(n))  # 代表元
        self.cc = n  # 连通块个数
    # 返回 x 所在集合的代表元
    # 同时做路径压缩，也就是把 x 所在集合中的所有元素的 fa 都改成代表元
    def find(self, x: int) -> int:
        # 如果 fa[x] == x，则表示 x 是代表元
        if self._fa[x] != x:
            self._fa[x] = self.find(self._fa[x])  # fa 改成代表元
        return self._fa[x]
    # 把 from 所在集合合并到 to 所在集合中
    # 返回是否合并成功
    def merge(self, from_: int, to: int) -> bool:
        x, y = self.find(from_), self.find(to)
        if x == y:  # from 和 to 在同一个集合，不做合并
            return False
        self._fa[x] = y  # 合并集合。修改后就可以认为 from 和 to 在同一个集合了
        self.cc -= 1  # 成功合并，连通块个数减一
        return True
# 计算图的最小生成树的边权之和
# 如果图不连通，返回 math.inf
# 节点编号从 0 到 n-1
# 时间复杂度 O(n + mlogm)，其中 m 是 edges 的长度
def mstKruskal(n: int, edges: List[List[int]]) -> int:
    edges.sort(key=lambda e: e[2])

    uf = UnionFind(n)
    sum_wt = 0
    for x, y, wt in edges:
        if uf.merge(x, y):
            sum_wt += wt

    if uf.cc > 1:  # 图不连通
        return inf
    return sum_wt
```
```python
class Solution:   #Prim  适合稠密图
    def minCostConnectPoints(self, points: List[List[int]]) -> int:
        n = len(points)
        if n <= 1:
            return 0
        # 记录每个点到当前生成树的最小距离
        min_dist = [float('inf')] * n
        in_mst = [False] * n
        # 从第 0 个点开始
        min_dist[0] = 0
        total_cost = 0
        for _ in range(n):
            # 找到距离生成树最近的点
            u = -1
            for i in range(n):
                if not in_mst[i] and (u == -1 or min_dist[i] < min_dist[u]):
                    u = i
            # 将该点加入生成树
            in_mst[u] = True
            total_cost += min_dist[u]
            # 更新其他点到生成树的距离
            for v in range(n):
                if not in_mst[v]:
                    dist = abs(points[u][0] - points[v][0]) + abs(points[u][1] - points[v][1])
                    if dist < min_dist[v]:
                        min_dist[v] = dist
        return total_cost
        
#prim的堆实现
def prim(n, graph):
    # graph: 邻接表，graph[u] = [(v, weight), ...]
    visited = [False] * n
    min_heap = [(0, 0, -1)]  # (weight, current_node, parent)
    mst = []
    total_weight = 0
    while min_heap and len(mst) < n:
        weight, u, parent = heapq.heappop(min_heap)
        if visited[u]:
            continue
        visited[u] = True
        if parent != -1:
            mst.append((parent, u, weight))
            total_weight += weight
        for v, w in graph[u]:
            if not visited[v]:
                heapq.heappush(min_heap, (w, v, u))
    return mst, total_weight
```
```python
import sys  #0-1最小生成树
from collections import deque
data=sys.stdin.read().strip().split('\n')
n,m=map(int,data[0].split())
g=[set() for _ in range(n)]
for x in data[1:]:
    u,v=map(int,x.split())
    u,v=u-1,v-1
    g[u].add(v)
    g[v].add(u)
unvisited=set(range(n))
connect=0
for start in range(n):
    if start not in unvisited:
        continue
    connect+=1
    q=deque([start])
    unvisited.remove(start)
    while q:
        u=q.popleft()
        for v in list(unvisited-g[u]):
            unvisited.remove(v)
            q.append(v)
print(connect-1)
```
## 哈夫曼树
在编码中没有任何码字是另⼀个码字的前缀。这样的编码被称为前缀编码。
⼆叉树 T 。 T 中的每条边代表码字中的⼀位，到左孩⼦的边代表“0”，到右孩⼦的边代表“1”。
每个叶⼦ v 与特定字符关联，该字符的码字由从 T 的根到 v 路径上的边所关联的⽐特序列定义。
每个叶⼦ v 都有⼀个频率 f(v) ，即与v关联的字符在 X 中的频率。此外，我们给T中的每个内部节点v赋予⼀个频率f(v)，它是以v为根的⼦树中所有叶⼦的频率之和。
霍夫曼编码算法从字符串X中每个独特的d个字符开始，每个字符都是单节点⼆叉树的根节点。算法以⼀系列的轮次进⾏。在每⼀轮中，算法将具有最⼩频率的两棵⼆叉树合并为⼀棵⼆叉树。此过程重复进⾏，直到只剩下⼀棵树为⽌。
霍夫曼编码（Huffman Coding）。要构造⼀棵 扩充⼆叉树（即每个⾮叶⼦节点都有两个⼦节点），使得所有 叶⼦节点的带权路径⻓度和最⼩。
```python
import sys,heapq  
class Node:  
    def __init__(self,c,w,l=None,r=None):  
        self.c,self.w,self.l,self.r=c,w,l,r  
        self.mc=c if c else min(l.mc,r.mc)  
    def __lt__(self, other):  
        return (self.w,self.mc)<(other.w,other.mc)  
def build_tree(arr):  
    heap=[]  
    for c,w in arr:  
        heap.append(Node(c,int(w)))  
    heapq.heapify(heap)  
    while len(heap)>1:  
        l=heapq.heappop(heap)  
        r=heapq.heappop(heap)  
        heapq.heappush(heap,Node(None,l.w+r.w,l,r))  
    return heap[0]  
def build_code(root,way):  
    if not root.l and not root.r:  
        char[root.c]=way  
    if root.l:  
        build_code(root.l, way+'0')  
    if root.r:  
        build_code(root.r, way+'1')  
def decode(s):  
    return ''.join(char[item] for item in s)  
def codeing(x):  
    cur=root  
    s=''  
    for i in x:  
        if i=='0':  
            cur=cur.l    
        else:  
            cur = cur.r  
        if cur.c:  
            s += cur.c  
            cur = root  
    return s  
data=sys.stdin.read().split()  
n=int(data[0])  
root=build_tree([(data[i],int(data[i+1]))for i in range(1,2*n,2)])  
char=dict()  
build_code(root,'')  
ans=[]  
for i in range(2*n+1,len(data)):  
    if data[i].isalpha():  
        ans.append(decode(data[i]))  
    else:  
        ans.append(codeing(data[i]))  
print('\n'.join(ans))
```
## LCA
要求0是根节点，否则要通过入度为0先找到根节点，再从根节点开始递归
```python
class LcaBinaryLifting:
    def __init__(self, edges: List[List[int]]):  #提交时别加后面注释
        n = len(edges) + 1
        m = n.bit_length()
        g = [[] for _ in range(n)]
        for x, y, w in edges:
            # 如果题目的节点编号从 1 开始，改成 x-1 和 y-1
            g[x].append((y, w))
            g[y].append((x, w))
        depth = [0] * n
        dis = [0] * n  # 如果是无权树（边权为 1），dis 可以去掉，用 depth 代替
        pa = [[-1] * n for _ in range(m)]
        def dfs(x: int, fa: int) -> None:
            pa[0][x] = fa
            for y, w in g[x]:
                if y != fa:
                    depth[y] = depth[x] + 1
                    dis[y] = dis[x] + w
                    dfs(y, x)
        dfs(0, -1)   
        for i in range(m - 1):
            for x in range(n):
                if (p := pa[i][x]) != -1:
                    pa[i + 1][x] = pa[i][p]
        self.depth = depth
        self.dis = dis
        self.pa = pa
    # 返回 node 的第 k 个祖先节点
    # 如果不存在，返回 -1
    def get_kth_ancestor(self, node: int, k: int) -> int:
        pa = self.pa
        for i in range(k.bit_length()):
            if k >> i & 1:
                node = pa[i][node]
                if node < 0: 
                    return -1
        return node
    # 返回 x 和 y 的最近公共祖先
    def get_lca(self, x: int, y: int) -> int:
        if self.depth[x] > self.depth[y]:
            x, y = y, x
        # 使 y 和 x 在同一深度
        y = self.get_kth_ancestor(y, self.depth[y] - self.depth[x])
        if y == x:
            return x
        pa = self.pa
        for i in range(len(pa) - 1, -1, -1):
            px, py = pa[i][x], pa[i][y]
            if px != py:
                x, y = px, py  # 同时往上跳 2**i 步
        return pa[0][x]
    # 返回 x 到 y 的距离（最短路长度）
    def get_dis(self, x: int, y: int) -> int:
        return self.dis[x] + self.dis[y] - self.dis[self.get_lca(x, y)] * 2
```
## ST表
```python
class SparseTable:
    # 时间复杂度 O(n * log n)
    def __init__(self, nums: List[int], op: Callable[[int, int], int]):
        n = len(nums)
        w = n.bit_length()
        st = [[0] * n for _ in range(w)]
        st[0] = nums[:]
        for i in range(1, w):
            for j in range(n - (1 << i) + 1):
                st[i][j] = op(st[i - 1][j], st[i - 1][j + (1 << (i - 1))])
        self.st = st
        self.op = op
    # [l, r) 左闭右开，下标从 0 开始
    # 返回 op(nums[l:r])
    # 时间复杂度 O(1)
    def query(self, l: int, r: int) -> int:
        k = (r - l).bit_length() - 1
        return self.op(self.st[k][l], self.st[k][r - (1 << k)])
# 使用方法举例
nums = [3, 1, 4, 1, 5, 9, 2, 6]
st = SparseTable(nums, max)
print(st.query(0, 5))  # max(nums[0:5]) = 5
print(st.query(1, 1))  # 错误：必须保证 l < r
```
## 最小斯坦纳树
```python
import sys
import heapq
INF = 10**18
def main():
    input = sys.stdin.read
    data = input().split()
    idx = 0
    n = int(data[idx]); idx += 1
    m = int(data[idx]); idx += 1
    k = int(data[idx]); idx += 1
    # 建图
    g = [[] for _ in range(n + 1)]
    for _ in range(m):
        u = int(data[idx]); idx += 1
        v = int(data[idx]); idx += 1
        w = int(data[idx]); idx += 1
        g[u].append((v, w))
        g[v].append((u, w))
    # 关键点编号映射到 0..k-1
    key = [int(data[idx + i]) for i in range(k)]
    idx += k
    # dp[mask][u] = 最小代价
    dp = [[INF] * (n + 1) for _ in range(1 << k)]
    # 初始化：单个关键点
    for i, node in enumerate(key):
        dp[1 << i][node] = 0
    # 子集DP + 最短路
    for mask in range(1, 1 << k):
        # 1) 子集合并
        # 注意：这里的子集枚举方式保证不重复合并
        sub = (mask - 1) & mask
        while sub > 0:
            rest = mask ^ sub
            for u in range(1, n + 1):
                if dp[sub][u] + dp[rest][u] < dp[mask][u]:
                    dp[mask][u] = dp[sub][u] + dp[rest][u]
            sub = (sub - 1) & mask
        # 2) 最短路松弛（类似Steiner树的标准做法）
        # Dijkstra 对同一 mask 的所有点
        dist = [INF] * (n + 1)
        for u in range(1, n + 1):
            dist[u] = dp[mask][u]
        pq = []
        for u in range(1, n + 1):
            if dist[u] < INF:
                heapq.heappush(pq, (dist[u], u))
        while pq:
            d, u = heapq.heappop(pq)
            if d != dist[u]:
                continue
            for v, w in g[u]:
                nd = d + w
                if nd < dist[v]:
                    dist[v] = nd
                    heapq.heappush(pq, (nd, v))
        # 更新回 dp
        for u in range(1, n + 1):
            if dist[u] < dp[mask][u]:
                dp[mask][u] = dist[u]
    full = (1 << k) - 1
    ans = min(dp[full][u] for u in range(1, n + 1))
    print(ans)

if __name__ == "__main__":
    main()
```

## 埃氏筛
```python
def countPrimes(self, n: int) -> int:  返回所有小于非负整数n的质数的数量。
	if n<=2:
		return 0
	is_prime=[True]*n
	is_prime[0]=is_prime[1]=False
	for i in range(2,n-1):
		if is_prime[i]:
			for j in range(i*i,n,i):
				is_prime[j]=False
	return sum(is_prime)
	
# 预处理每个数的质因子列表，思路同埃氏筛
MX = 1_000_001
prime_factors = [[] for _ in range(MX)]
for i in range(2, MX):
    if not prime_factors[i]:  # i 是质数
        for j in range(i, MX, i):  # i 的倍数有质因子 i
            prime_factors[j].append(i)
```
## 前缀树
```python
class Node:  
    def __init__(self):  
        self.children = {}  
        self.end = False
class Trie:  
    def __init__(self):  
        self.root = Node()  
    def insert(self, word: str) -> None:  
        node = self.root  
        for x in word:  
            if x not in node.children:  
                node.children[x] = Node()  
            node = node.children[x]  
        node.end = True  
    def search(self, word: str) -> bool:  
        node = self.root  
        for x in word:  
            if x not in node.children:  
                return False  
            node = node.children[x]  
        return True if node.end else False  
    def startsWith(self, prefix: str) -> bool:  
        node = self.root  
        for x in prefix:  
            if x not in node.children:  
                return False  
            node = node.children[x]  
        return True
        
class Node:   #维护最长相同后缀，长度最短，下标最小的下标。
    def __init__(self):
        self.children = {}
        self.min_len=float('inf')
        self.best_index=None
class Trie:
    def __init__(self):
        self.root = Node()
    def insert(self, word: str,i) -> None:
        len_word=len(word)
        node = self.root
        if len_word<node.min_len:
                node.min_len=len_word
                node.best_index=i
        for x in word:
            if x not in node.children:
                node.children[x] = Node()
            node = node.children[x]
            if len_word<node.min_len:
                node.min_len=len_word
                node.best_index=i
class Solution:
    def stringIndices(self, wordsContainer: List[str], wordsQuery: List[str]) -> List[int]:
        wordsContainer=[x[::-1] for x in wordsContainer]
        wordsQuery=[x[::-1] for x in wordsQuery]
        t=Trie()
        for i,x in enumerate(wordsContainer):
            t.insert(x,i)
        ans=[]
        for x in wordsQuery:
            cur=t.root
            for ch in x:
                if ch not in cur.children:
                    break
                cur=cur.children[ch]
            ans.append(cur.best_index)
        return ans
```
## 栈
```python
def isValid(s: str) -> bool:    #括号匹配
    stack = []  
    match = {')': '(', ']': '[', '}': '{'}  
    for ch in s:  
        if ch in '({[':  # 左括号压栈  
            stack.append(ch)  
        else:  # 右括号  
            if not stack or stack[-1] != match[ch]:  
                return False  
            stack.pop()  
    return not stack  # 栈空则全部匹配  
print(isValid("()[]{}"))  # True  
print(isValid("([)]"))  # False

def convert_base(num: int, base: int) -> str:#将十进制转换为任意进制(2-16)
    if num == 0:
        return "0"
    digits = "0123456789ABCDEF"
    stack = []
    while num > 0:
        stack.append(digits[num % base])  # 余数压栈
        num //= base 
    result ='' # 出栈拼接
    while stack:
        result += stack.pop()
    return result
print(convert_base(10, 2))   # "1010"
print(convert_base(255, 16)) # "FF"

def evalRPN(tokens: list) -> int:
    stack = []
    for token in tokens:
        if token in '+-*/':
            b = stack.pop()  # 注意顺序：先弹出的是右操作数
            a = stack.pop()
            if token == '+':
                stack.append(a + b)
            elif token == '-':
                stack.append(a - b)
            elif token == '*':
                stack.append(a * b)
            else:  # '/'
                stack.append(int(a / b))  # 向零取整
        else:
            stack.append(int(token))
    return stack[0]
# 测试
print(evalRPN(["2","1","+","3","*"]))     # 9  (2+1)*3
print(evalRPN(["4","13","5","/","+"]))    # 6  4+13/5

n=int(input())  #中序表达式转后序表达式
def transfer(alist):  
    ans=[]  st=[]  prio=dict()  prio['(']=1  prio['+']=2  prio['-']=2  prio['*']=3  prio['/']=3  
    for token in alist:  
        if token=='(':  
            st.append(token)  
        elif token==')':  
            item=st.pop()  
            while item !='(':  
                ans.append(item)  
                item=st.pop()  
        elif token in '+-*/':  
            while st and prio[st[-1]]>=prio[token]:  
                ans.append(st.pop())  
            st.append(token)  
        else:  
            ans.append(token)  
    while st:  
        ans.append(st.pop())  
    return ' '.join(ans)  
for _ in range(n):  
    alist=input()  
    alist=alist.replace('+',' + ').replace('-',' - ')  
    alist=alist.replace('*',' * ').replace('/',' / ')  
    alist=alist.replace('(',' ( ').replace(')',' ) ')  
    alist=alist.split()  
    print(transfer(alist))
```
```python
```python
n=int(input())  #出栈序列统计
st=[]
out=[]
wait=[i for i in range(n,0,-1)]
ans=0
def dfs():
    global ans
    if len(out)==n:
        ans+=1
        return
    if st:
        a = st.pop()
        out.append(a)
        dfs()
        out.pop()
        st.append(a)
    if wait:
        b = wait.pop()
        st.append(b)
        dfs()
        st.pop()
        wait.append(b)
dfs()
print(ans)
```
```python
def judge_pop(x,seq):  #合法出栈序列
    st=[]  
    it=iter(x)  
    for ch in seq:  
        while (not st or st[-1]!=ch) and ((ch_in:=next(it,None)) is not None):  
            st.append(ch_in)  
        if st and st[-1]==ch:  
            st.pop()  
        else:  
            return False  
    return True  
x=input()  
n=len(x)  
try:  
    while 1:  
        seq=input()  
        if seq=='' or len(seq)!=n:  
            print('NO')  
            continue  
        print('YES') if judge_pop(x,seq) else print('NO')  
except:  
    pass
```
## 字符串
kmp
```python
# 在文本串 text 中查找模式串 pattern，返回所有成功匹配的位置（pattern[0] 在 text 中的下标）
def kmp(text: str, pattern: str) -> List[int]:
    m = len(pattern)
    pi = [0] * m   #pi[i]  为pattern[:i]这个字符串最长匹配的真前缀和真后缀
    cnt = 0
    for i in range(1, m):
        b = pattern[i]
        while cnt and pattern[cnt] != b:
            cnt = pi[cnt - 1]
        if pattern[cnt] == b:
            cnt += 1
        pi[i] = cnt
    pos = []
    cnt = 0
    for i, b in enumerate(text):
        while cnt and pattern[cnt] != b:
            cnt = pi[cnt - 1]
        if pattern[cnt] == b:
            cnt += 1
        if cnt == len(pattern):
            pos.append(i - m + 1)
            cnt = pi[cnt - 1]
    return pos
```
对于长度为 `n` 的字符串 `s`，如果 **`n % (n - LPS[n-1]) == 0`** 且 `LPS[n-1] != 0`，那么：
最小循环节长度 = n - LPS\[n-1].  有循环节时，字符串由 n // (n - LPS\[n-1])个循环节重复组成。 
如果不整除，则字符串没有完整的循环节（或者说最小循环节就是它自身）。（LPS就是上面的pi）
z函数
```python
# 计算并返回 z 数组，其中 z[i] = |LCP(s[i:], s)|
def calc_z(s: str) -> List[int]:    #Z函数
    n = len(s)
    z = [0] * n
    box_l = box_r = 0
    for i in range(1, n):
        if i <= box_r:
            z[i] = min(z[i - box_l], box_r - i + 1)
        while i + z[i] < n and s[z[i]] == s[i + z[i]]:
            box_l, box_r = i, i + z[i]
            z[i] += 1
    z[0] = n
    return z
```
马拉车
```python
class Solution:  #马拉车
    def longestPalindrome(self, s: str) -> str:
        # Manacher 模板
        # 将 s 改造为 t，这样就不需要讨论 len(s) 的奇偶性，因为新串 t 的每个回文子串都是奇回文串（都有回文中心）
        # s 和 t 的下标转换关系：
        # (si+1)*2 = ti
        # ti/2-1 = si
        # ti 为偶数，对应奇回文串（从 2 开始）
        # ti 为奇数，对应偶回文串（从 3 开始）
        t = "#".join("^" + s + "$")
        # 定义一个奇回文串的回文半径=(长度+1)/2，即保留回文中心，去掉一侧后的剩余字符串的长度
        # half_len[i] 表示在 t 上的以 t[i] 为回文中心的最长回文子串的回文半径
        # 即 [i-half_len[i]+1,i+half_len[i]-1] 是 t 上的一个回文子串
        half_len = [0] * (len(t) - 2)
        half_len[1] = 1
        # box_r 表示当前右边界下标最大的回文子串的右边界下标+1
        # box_m 为该回文子串的中心位置，二者的关系为 r=mid+half_len[mid]
        box_m = box_r = max_i = 0
        for i in range(2, len(half_len)):
            hl = 1
            if i < box_r:
                # 记 i 关于 box_m 的对称位置 i'=box_m*2-i
                # 若以 i' 为中心的最长回文子串范围超出了以 box_m 为中心的回文串的范围（即 i+half_len[i'] >= box_r）
                # 则 half_len[i] 应先初始化为已知的回文半径 box_r-i，然后再继续暴力匹配
                # 否则 half_len[i] 与 half_len[i'] 相等
                hl = min(half_len[box_m * 2 - i], box_r - i)  
            # 暴力扩展
            # 算法的复杂度取决于这部分执行的次数
            # 由于扩展之后 box_r 必然会更新（右移），且扩展的的次数就是 box_r 右移的次数
            # 因此算法的复杂度 = O(len(t)) = O(n)
            while t[i - hl] == t[i + hl]:
                hl += 1
                box_m, box_r = i, i + hl         
            half_len[i] = hl
            if hl > half_len[max_i]:
                max_i = i
        hl = half_len[max_i]
        # 注意 t 上的最长回文子串的最左边和最右边都是 '#'
        # 所以要对应到 s，最长回文子串的下标是从 max_i-hl+2 到 max_i+hl-2
        # 结合上文的下标转换关系，得到其在 s 上的下标范围是从 (max_i-hl)/2 到 (max_i+hl)/2-2
        return s[(max_i - hl) // 2: (max_i + hl) // 2 - 1]
```
双模哈希
```python
# 由于乘法超过 64 位整数范围，需要用到 bigint，所以效率不如写法一
class Solution:
    def minimumCost(self, target: str, words: List[str], costs: List[int]) -> int:
        n = len(target)

        # 多项式字符串哈希（方便计算子串哈希值）
        # 哈希函数 hash(s) = s[0] * BASE^(n-1) + s[1] * BASE^(n-2) + ... + s[n-2] * BASE + s[n-1]
        MOD = 10 ** 18 + 3
        BASE = randint(8 * 10 ** 17, 9 * 10 ** 17)  # 随机 BASE，防止 hack
        pow_base = [1] + [0] * n  # pow_base[i] = BASE^i
        pre_hash = [0] * (n + 1)  # 前缀哈希值 pre_hash[i] = hash(s[:i])
        for i, b in enumerate(target):
            pow_base[i + 1] = pow_base[i] * BASE % MOD
            pre_hash[i + 1] = (pre_hash[i] * BASE + ord(b)) % MOD  # 秦九韶算法计算多项式哈希

        # 每个 words[i] 的哈希值 -> 最小成本
        min_cost = defaultdict(lambda: inf)
        for w, c in zip(words, costs):
            h = 0
            for b in w:
                h = (h * BASE + ord(b)) % MOD
            min_cost[h] = min(min_cost[h], c)

        # 有 O(√L) 个不同的长度
        sorted_lens = sorted(set(map(len, words)))

        f = [0] + [inf] * n
        for i in range(1, n + 1):
            for sz in sorted_lens:
                if sz > i:
                    break
                # 计算子串 target[i-sz:i] 的哈希值（计算方法类似前缀和）
                sub_hash = (pre_hash[i] - pre_hash[i - sz] * pow_base[sz]) % MOD
                # 手写 min，避免超时
                tmp = f[i - sz] + min_cost[sub_hash]
                if tmp < f[i]:
                    f[i] = tmp
        return -1 if f[n] == inf else f[n]
```
ac自动机
```python
class Node:
    __slots__ = 'son', 'fail', 'last', 'len', 'cost'
    def __init__(self):
        self.son = [None] * 26
        self.fail = None  # 当 cur.son[i] 不能匹配 target 中的某个字符时，cur.fail.son[i] 即为下一个待匹配节点（等于 root 则表示没有匹配）
        self.last = None  # 后缀链接（suffix link），用来快速跳到一定是某个 words[k] 的最后一个字母的节点（等于 root 则表示没有）
        self.len = 0
        self.cost = inf
class AhoCorasick:
    def __init__(self):
        self.root = Node()
    def put(self, s: str, cost: int) -> None:
        cur = self.root
        for b in s:
            b = ord(b) - ord('a')
            if cur.son[b] is None:
                cur.son[b] = Node()
            cur = cur.son[b]
        cur.len = len(s)
        cur.cost = min(cur.cost, cost)
    def build_fail(self) -> None:
        self.root.fail = self.root.last = self.root
        q = deque()
        for i, son in enumerate(self.root.son):
            if son is None:
                self.root.son[i] = self.root
            else:
                son.fail = son.last = self.root  # 第一层的失配指针，都指向根节点 ∅
                q.append(son)
        # BFS
        while q:
            cur = q.popleft()
            for i, son in enumerate(cur.son):
                if son is None:
                    # 虚拟子节点 cur.son[i]，和 cur.fail.son[i] 是同一个
                    # 方便失配时直接跳到下一个可能匹配的位置（但不一定是某个 words[k] 的最后一个字母）
                    cur.son[i] = cur.fail.son[i]
                    continue
                son.fail = cur.fail.son[i]  # 计算失配位置
                # 沿着 last 往上走，可以直接跳到一定是某个 words[k] 的最后一个字母的节点（如果跳到 root 表示没有匹配）
                son.last = son.fail if son.fail.len else son.fail.last
                q.append(son)
class Solution:
    def minimumCost(self, target: str, words: List[str], costs: List[int]) -> int:
        ac = AhoCorasick()
        for w, c in zip(words, costs):
            ac.put(w, c)
        ac.build_fail()
        n = len(target)
        f = [0] + [inf] * n
        cur = root = ac.root
        for i in range(1, n + 1):
            cur = cur.son[ord(target[i - 1]) - ord('a')]  # 如果没有匹配相当于移动到 fail 的 son[target[i-1]-'a']
            if cur.len:  # 匹配到了一个尽可能长的 words[k]
                f[i] = min(f[i], f[i - cur.len] + cur.cost)
            # 还可能匹配其余更短的 words[k]，要在 last 链上找
            match_node = cur.last
            while match_node != root:
                # 手写 min 更快
                tmp = f[i - match_node.len] + match_node.cost
                if tmp < f[i]:
                    f[i] = tmp
                match_node = match_node.last
        return -1 if f[n] == inf else f[n]
```
## 欧拉路径
```python
import sys
from collections import defaultdict, deque
sys.setrecursionlimit(3000)
data=sys.stdin.read().strip().split()
t=int(data[0])
idx=1
result=[]
for _ in range(t):
    n=int(data[idx])
    idx+=1
    g=defaultdict(list)
    g1=defaultdict(list)
    testg=defaultdict(list)
    in_degree=defaultdict(int)
    out_degree=defaultdict(int)
    nodes=set()
    words=[]
    for i in range(idx,idx+n):
        s=data[i]
        words.append(s)
        nodes.add(s[0])
        nodes.add(s[-1])
        in_degree[s[-1]]+=1
        out_degree[s[0]]+=1
        g[s[0]].append((s[-1],s))
        g1[(s[0], s[-1])].append(s)
        testg[s[0]].append(s[-1])
        testg[s[-1]].append(s[0])
    idx+=n
    find=True
    for u in g:
        g[u].sort(key=lambda x:x[1],reverse=True)
    for node in g1:
        g1[node].sort(reverse=True)
    cnt_out=0
    cnt_in=0
    start=None
    for node in nodes:
        diff=out_degree[node]-in_degree[node]
        if diff==1:
            cnt_out+=1
            start=node
        elif diff==-1:
            cnt_in+=1
        elif diff!=0:
            find=False
            break
    if not find or (not ((cnt_out==1 and cnt_in==1) or (cnt_out==0 and cnt_in==0))):
        result.append('***')
        continue
    if start is None:
        start=min(nodes)
    visited=set(start)#bfs检测弱连通性
    q=deque([start])
    while q:
        node=q.popleft()
        for nxt in testg[node]:
            if nxt not in visited:
                q.append(nxt)
                visited.add(nxt)
    if len(visited)!=len(testg):
        result.append('***')
        continue
    path=[]
    def dfs(node):
        while g[node]:
            nxt,_=g[node].pop()
            dfs(nxt)
        path.append(node)
    dfs(start)
    path.reverse()
    ans=['']*(len(path)-1)
    for i in range(len(path)-1):
        edge=g1[(path[i],path[i+1])].pop()
        ans[i]=edge
    result.append('.'.join(ans))
print('\n'.join(result))
```
## 链表
```python
def reverseList(self, head: Optional[ListNode]) -> Optional[ListNode]:#反转链表
	pre = None
	cur = head
	while cur:
		nxt = cur.next
		cur.next = pre  # 把 cur 插在 pre 链表的前面（头插法）
		pre = cur
		cur = nxt
	return pre
def reverseKGroup(self, head: Optional[ListNode], k: int) -> Optional[ListNode]:
	n = 0   cur = head  # 统计节点个数 
	while cur:
		n += 1
		cur = cur.next
	p0 = dummy = ListNode(next=head)
	pre = None    cur = head
	while n >= k:# k 个一组处理
		n -= k
		for _ in range(k):  #k个一组翻转
			nxt = cur.next
			cur.next = pre  # 每次循环只修改一个 next，方便大家理解
			pre = cur
			cur = nxt
		nxt = p0.next
		nxt.next = cur
		p0.next = pre
		p0 = nxt
	return dummy.next
def middleNode(self, head: Optional[ListNode]) -> Optional[ListNode]:
	slow = fast = head  #快慢指针求中间节点
	while fast and fast.next:
		slow = slow.next  # 乌龟走一步
		fast = fast.next.next  # 兔子走两步
	return slow  # 兔子到达终点时，乌龟走了一半，在中间位置
def hasCycle(self, head: Optional[ListNode]) -> bool:#判断有没有环
	slow = fast = head  # 乌龟和兔子同时从起点出发
	while fast and fast.next:
		slow = slow.next  # 乌龟走一步
		fast = fast.next.next  # 兔子走两步
		if fast is slow:  # 兔子追上乌龟（套圈），说明有环
			return True
	return False  # 访问到了链表末尾，无环
def detectCycle(self, head: Optional[ListNode]) -> Optional[ListNode]:
	slow = fast = head  #有环则给出相遇地点
	while fast and fast.next:
		slow = slow.next
		fast = fast.next.next
		if fast is slow:  # 相遇
			while slow is not head:  # 再走 a 步
				slow = slow.next
				head = head.next
			return slow
	return None
def removeNthFromEnd(self, head: Optional[ListNode], n: int) -> Optional[ListNode]:
	left = right = dummy = ListNode(next=head)#删除链表的倒数第n个结点
	for _ in range(n):# 由于可能会删除链表头部，用哨兵节点简化代码
		right = right.next  # 右指针先向右走 n 步
	while right.next:
		left = left.next
		right = right.next  # 左右指针一起走
	left.next = left.next.next  # 左指针的下一个节点就是倒数第 n 个节点
	return dummy.next
def getIntersectionNode(self, headA: ListNode, headB: ListNode) -> Optional[ListNode]: #相交链表
	p, q = headA, headB
	while p is not q:
		p = p.next if p else headB
		q = q.next if q else headA
	return p
class Node:
    def __init__(self, val):
        self.val = val
        self.next = None
def josephus(n, m): # 构建循环链表
    head = Node(1)
    prev = head
    for i in range(2, n+1):
        node = Node(i)
        prev.next = node
        prev = node
    prev.next = head   # 尾部指向头部，形成环
    curr = head  # 模拟淘汰
    while curr.next != curr:  # 只剩一个节点时退出
        # 走 m-1 步
        for _ in range(m-1):
            curr = curr.next
        # 删除 curr 的下一个节点
        curr.next = curr.next.next
        # 移动到下一个开始报数的人
        curr = curr.next
    return curr.val
print(josephus(5, 3))  # 输出 1
def josephus_math(n, m):  #与上面约定有点不同，看题目决定
    survivor = 0  # n=1 时，幸存者编号 0
    for i in range(2, n+1):  # 从 2 个人递推到 n 个人
        survivor = (survivor + m) % i
    return survivor + 1  # 如果题目要求编号从 1 开始
print(josephus_math(5, 3))  # 输出 4
```
## 树
```python
前中后序**dfs**   前序和后序也同理，只是需要改变函数中三行的顺序
def inorderTraversal(root):
    l=[]
    def dfs(lo):
        if lo:
            dfs(lo.left)
            l.append(lo.val)
            dfs(lo.right)
    dfs(root)
    return l
```
```python
dfn = [0] * num  #一般树的dfs序
dn = 0
stack = [(S,0)]
vis = [False] * num
while stack:
    node, father = stack.pop()
    if vis[node]:
        continue
    vis[node] = True
    dn += 1
    dfn[node] = dn
    for child in reversed(graph[node]):
        if child != father and not vis[child]:
            stack.append((child, father))
```
```python
class Node:    #平衡二叉树的建立
    def __init__(self,val=None,l=None,r=None):  
        self.val=val  
        self.height=0  
        self.left,self.right=l,r  
def get_height(node):  
    return node.height if node else -1  
def update_height(node):  
    node.height=max(get_height(node.left),get_height(node.right))+1  
def balance_factor(node):  
    if node is None:  
        return 0  
    return get_height(node.left)-get_height(node.right)  
def insert(node,val):  
    if node is None:  
        return Node(val)  
    if val<node.val:  
        node.left=insert(node.left,val)  
    else:  
        node.right=insert(node.right,val)  
    if balance_factor(node)>1:    #L  
        if balance_factor(node.left)==1:    #LL  
            node=rotate_right(node)  
        elif balance_factor(node.left)==-1:  
            node.left=rotate_left(node.left)  
            node=rotate_right(node)  
    elif balance_factor(node)<-1:   #R  
        if balance_factor(node.right)==-1:   #RR  
            node=rotate_left(node)  
        elif balance_factor(node.right)==1:  
            node.right=rotate_right(node.right)  
            node=rotate_left(node)  
    update_height(node)  
    return node  
def rotate_left(node):  
    x = node.right  
    y = x.left  
    x.left = node  
    node.right = y  
    update_height(node)  
    update_height(x)  
    return x  
def rotate_right(node):  
    x=node.left  
    y=x.right  
    x.right=node  
    node.left=y  
    update_height(node)  
    update_height(x)  
    return x  
def preorder(node):  
    if node is None:  
        return  
    result.append(node.val)  
    preorder(node.left)  
    preorder(node.right)  
n=int(input())  
arr=list(map(int,input().split()))  
root=None  
for val in arr:  
    root=insert(root,val)  
result=[]  
preorder(root)  
print(' '.join(map(str,result)))
```
```python
def sortedArrayToBST(self, nums: List[int]) -> Optional[TreeNode]:
	def dfs(left,right):  #有序数组转化为二叉搜索树
		if left==right:
			return TreeNode(nums[left])
		if left+1==right:
			return TreeNode(nums[right],TreeNode(nums[left]))
		mid=(left+right)//2
		return TreeNode(nums[mid],dfs(left,mid-1),dfs(mid+1,right))
	n=len(nums)
	return dfs(0,n-1)
class TreeNode:
    def __init__(self,val,left=None,right=None):
        self.val=val
        self.left=left
        self.right=right
def insert(node,root):#无序数组转化为二叉搜索树
    if not root:
        return TreeNode(node)
    if node<root.val:
        root.left=insert(node,root.left)
    else:
        root.right=insert(node,root.right)
    return root
def to_pre(root):
    if root is None:
        return ''
    return root.val+to_pre(root.left)+to_pre(root.right)
import sys
data=sys.stdin.read().split('*')
n=len(data)
for tree in data:
    tree=tree.strip('$\n').split('\n')     #数据可能最后有换行，要一起去掉
    tree.reverse()
    root=None
    for row in tree:
        for item in row:
            root=insert(item,root)
    print(to_pre(root))

def lowestCommonAncestor(self, root:'TreeNode',p: 'TreeNode', q: 'TreeNode'):
	if root in (None, p, q):  # 找到 p 或 q 就不往下递归了，原因见上面答疑
		return root#二叉树最近公共祖先
	left = self.lowestCommonAncestor(root.left, p, q)
	right = self.lowestCommonAncestor(root.right, p, q)
	if left and right:  # 左右都找到
		return root  # 当前节点是最近公共祖先
	# 如果只有左子树找到，就返回左子树的返回值
	# 如果只有右子树找到，就返回右子树的返回值
	# 如果左右子树都没有找到，就返回 None（注意此时 right = None）
	return left or right
def lowestCommonAncestor(self, root: 'TreeNode', p: 'TreeNode', q: 'TreeNode'):
	x = root.val#二叉树搜索树最近公共祖先
	if p.val < x and q.val < x:  # p 和 q 都在左子树
		return self.lowestCommonAncestor(root.left, p, q)
	if p.val > x and q.val > x:  # p 和 q 都在右子树
		return self.lowestCommonAncestor(root.right, p, q)
	return root  # 其它
class Solution:#最深叶节点的最近公共祖先
    def height(self,root):
        if root is None:
            return -1
        return max(self.height(root.left),self.height(root.right))+1
    def lcaDeepestLeaves(self, root: Optional[TreeNode]) -> Optional[TreeNode]:
        height=self.height(root)
        def dfs(node,h):
            if node is None:
                return False        
            p=dfs(node.left,h+1)
            q=dfs(node.right,h+1)
            if p and q:
                return node
            if p:
                return p
            if q:
                return q
            else:
                if h==height:
                    return node
                else:
                    return False
        return dfs(root,0)
 
def build_postorder(preorder,inorder):  #根据二叉树前中序序列建树
    if not preorder:  
        return ''  
    node=preorder[0]  
    idx=inorder.index(node)  
    n=len(preorder)  
    left_pre=preorder[1:idx+1]  
    right_pre=preorder[idx+1:] if idx<n else ''  
    left_in=inorder[0:idx]  
    right_in=inorder[idx+1:] if idx<n else ''  
    left_post=build_postorder(left_pre,left_in)  
    right_post=build_postorder(right_pre,right_in)  
    return left_post+right_post+node  
data=sys.stdin.read().split('\n')  
m=len(data)//2  
ans=[]  
for index in range(m):  
    ans.append(build_postorder(data[index*2],data[index*2+1]))  
print('\n'.join(ans))


data=sys.stdin.read().split('\n')  #apple tree 从根节点开始遍历确定父子关系
t=int(data[0])    idx=1  
def build_tree(n,edges):    
    g=[[]for _ in range(n+1)]   #邻接表,用n+1就不用下标减1了  
    for u in edges:  
        u=u.split()  
        v=int(u[1])  
        u=int(u[0])  
        g[u].append(v)  
        g[v].append(u)  
    tree=[[]for _ in range(n+1)]  
    parent=[0]*(n+1)  
    def dfs(u,p):  
        for v in g[u]:  
            if v!=p:  
                tree[u].append(v)  
                parent[v]=u  
                dfs(v,u)  
    dfs(1,0)  
    return tree  
def chart(node):   #记录苹果从每个点开始掉的可能数  
    if tree[node]==[]:  
        achart[node]=1  
        return 1  
    else:  
        for child in tree[node]:  
            achart[node]+=chart(child)  
        return achart[node]  
for _ in range(t):  
    n=int(data[idx])  
    idx+=1  
    tree=build_tree(n,data[idx:idx+n-1])  
    achart=defaultdict(int)  
    chart(1)  
    idx+=(n-1)  
    q=int(data[idx])  
    for _ in range(q):  
        idx+=1  
        x=data[idx].split()  
        y=int(x[1])  
        x=int(x[0])  
        print(achart[x]*achart[y])  
    idx+=1
```
```python
n=int(input())  #向下调整构建大顶堆  #向下调整构建一般指从最后一个非叶子节点开始，逐个向上检查并向下调整，使整个树变成大顶堆。
heap=list(map(int,input().split()))  
def dfs(i):  #向下调整就是对于某个节点，如果它比它的某个子节点小，就把它和最大的那个子节点交换，然后继续检查被交换下去的子节点位置，直到没问题为止。
    val=heap[i]  
    left_son=heap[2*i+1]    if 2*i+1<n else -float('inf')  
    right_son=heap[2*i+2]   if 2*i+2<n else -float('inf')  
    if val>=left_son and val>=right_son:  
        return  
    elif left_son>right_son:  
        heap[i],heap[2*i+1]=heap[2*i+1],heap[i]  
        if 2*i+1<n:  
            dfs(2*i+1)  
    else:  
        heap[i], heap[2 * i + 2] = heap[2 * i + 2], heap[i]  
        if 2 * i + 2 < n:  
            dfs(2 * i + 2)  
for i in range((n//2)-1,-1,-1):  #最后一个非叶子节点索引 = n//2 - 1。
    dfs(i)  
print(' '.join(map(str,heap)))

def up_adjust(arr, i): #向上调整原理（也称“插入法建堆”）：
#- 从 数组第 2 个元素（即索引 1）开始遍历到末尾（n-1）
#对于每个节点，如果它比它的父节点大，就交换，继续与新的父节点比较，直到根或者不再大于父节点
#这个过程相当于**每次在已有的大顶堆尾部插入一个新节点，然后向上调整维持堆性质**
    # i 是当前新插入节点的索引，向上调整到正确位置
    while i > 0:
        parent = (i - 1) // 2
        if arr[i] > arr[parent]:
            arr[i], arr[parent] = arr[parent], arr[i]
            i = parent
        else:
            break
def build_max_heap_up(arr):
    n = len(arr)
    for i in range(1, n):
        up_adjust(arr, i)
n = int(input().strip())
arr = list(map(int, input().strip().split()))
build_max_heap_up(arr)
print(' '.join(map(str, arr)))

def heapify(arr, n, i):
    """向下调整，arr[0..n-1] 为堆数组，i 是要调整的节点"""
    largest = i
    left = 2 * i + 1
    right = 2 * i + 2
    if left < n and arr[left] > arr[largest]:
        largest = left
    if right < n and arr[right] > arr[largest]:
        largest = right
    if largest != i:
        arr[i], arr[largest] = arr[largest], arr[i]
        heapify(arr, n, largest)
def build_max_heap(arr):"""向下调整建堆"""
    n = len(arr)
    for i in range(n // 2 - 1, -1, -1): # 从最后一个非叶子节点开始
        heapify(arr, n, i)
def delete_top(arr):"""删除堆顶并调整"""
    n = len(arr)#现有​​​​​个不同的正整数，将它们按层序生成完全二叉树，
    if n == 0:#然后使用**向下调整**的方式构建一个完整的大顶堆。
        return []#然后删除堆顶元素，并将层序最后一个元素置于堆顶，进行一次向下调整，以形成新的堆。最后按层序输出新堆中的所有元素。
    arr[0] = arr[n - 1]# 将最后一个元素移到堆顶
    n -= 1# 减小堆大小
    heapify(arr, n, 0)# 对堆顶进行向下调整
    return arr[:n]# 返回调整后有效堆的列表（前 n 个元素）
n = int(input().strip())
arr = list(map(int, input().strip().split()))
build_max_heap(arr)
new_heap = delete_top(arr)
print(' '.join(map(str, new_heap)))
```
```python
import sys  #败方树的构建
from collections import deque
class TreeNode:
    def __init__(self,v=0,w=0):
        self.val=v    #输者，也是该处的节点值
        self.win=w
        self.left=None
        self.right=None
        self.parent=None    #建立parent指针方便更新时往上爬
data=sys.stdin.read().strip().split('\n')
n,m=map(int,data[0].split())
leaves=[TreeNode(int(v),int(v)) for v in data[1].split()]
q=deque(leaves)
while len(q)>1:
    l=q.popleft()
    r=q.popleft()
    winner=min(l.win,r.win)
    loser=max(l.win,r.win)
    node=TreeNode(loser,winner)
    node.left,node.right=l,r
    l.parent=r.parent=node
    q.append(node)
second=q.popleft()
root=TreeNode(second.win,second.win)
second.parent=root
root.left=second
def print_inner():
    vals=[]
    q=deque([root])
    while q:
        if len(vals)==n:
            print(' '.join(map(str,vals)))
            break
        node=q.popleft()
        vals.append(node.val)
        if node.left:
            q.append(node.left)
        if node.right:
            q.append(node.right)
print_inner()
def update(idx,val):
    start=leaves[idx]
    start.val=start.win=val
    cur=start.parent
    while 1:
        if not cur.right:
            break       cur.val,cur.win=max(cur.left.win,cur.right.win),min(cur.left.win,cur.right.win)
        cur=cur.parent
    cur.val=cur.win=cur.left.win
    print_inner()
for x in data[2:]:
    idx,val=map(int,x.split())
    update(idx,val)
```
## 森林
```python
from collections import deque  ### 森林的带度数层次序列存储
import sys  
class TreeNode:  
    def __init__(self,name,degree):  
        self.name=name  
        self.degree=degree  
        self.children=[]  
def build_tree(seq):  
    root=TreeNode(seq[0],int(seq[1]))  
    i=2  
    q=deque([root])  
    while i<len(seq):  
        name=seq[i]  
        i+=1  
        degree=int(seq[i])  
        i+=1  
        node=TreeNode(name,degree)  
        if q:  
            father=q[0]  
            father.children.append(node)  
            if len(father.children)==father.degree:  
                q.popleft()  
        if degree>0:    q.append(node)   #只让需要孩子的节点入队  
    return root  
def back(tree,ans):  
    if tree==None:  
        return  
    for children in tree.children:  
        back(children,ans)  
    ans.append(tree.name)  
    return  
n=int(sys.stdin.readline())  
result=[]  
for _ in range(n):  
    seq=sys.stdin.readline().split()  
    tree=build_tree(seq)  
    back(tree,result)  
print(' '.join(result))
```
森林转化为二叉树
1. **左指针**指向该节点的**第一个孩子**
2. **右指针**指向该节点的**下一个兄弟**
- 森林的**先根遍历** = 二叉树的**先序遍历**
- 森林的**后根遍历** = 二叉树的**中序遍历**
对于普通树（每个节点可以有多个孩子），**后根遍历**的定义是：
**依次对每个子树进行后根遍历 → 最后访问根节点**
**没有“右子树”的概念**，因为孩子是多个，按从左到右的顺序处理
对于一棵普通的树（多叉树），**前根遍历**（也称先根遍历）的定义是：
**首先访问根节点，然后从左到右依次对每棵子树进行前根遍历。**
```python
from typing import List, Optional  
class BTreeNode:  # 二叉树节点  
    def __init__(self, val: int):  
        self.val = val  
        self.left = None  
        self.right = None  
class TreeNode:  # 多叉树节点  
    def __init__(self, val: int):  
        self.val = val  
        self.children = []  # 孩子列表  
# 将一棵多叉树转换为二叉树（左孩子右兄弟）  
def tree_to_binary(root: Optional[TreeNode]) -> Optional[BTreeNode]:  
    if not root:  
        return None  
    bin_root = BTreeNode(root.val)  
    # 左孩子指向第一个子节点转换后的二叉树  
    if root.children:  
        bin_root.left = tree_to_binary(root.children[0])  
    # 右孩子指向下一个兄弟（用循环处理多个孩子）  
    cur = bin_root.left  
    for i in range(1, len(root.children)):  
        cur.right = tree_to_binary(root.children[i])  
        cur = cur.right  
    return bin_root  
# 将森林（多棵树）转换为二叉树  
def forest_to_binary(forest: List[TreeNode]) -> Optional[BTreeNode]:  
    if not forest:  
        return None  
    # 第一棵树的根作为整个二叉树的根  
    root = tree_to_binary(forest[0])  
    # 后续树的根依次作为前一棵树根的右兄弟  
    cur = root  
    for i in range(1, len(forest)):  
        cur.right = tree_to_binary(forest[i])  
        cur = cur.right  
    return root  
# 辅助：中序遍历（验证森林后根遍历 = 二叉树中序遍历）  
def inorder(root: Optional[BTreeNode]):  
    if not root:  
        return  
    inorder(root.left)  
    print(root.val, end=" ")  
    inorder(root.right)  
# 辅助：先序遍历（验证森林前根遍历 = 二叉树先序遍历）  
def preorder(root: Optional[BTreeNode]):  
    if not root:  
        return  
    print(root.val, end=" ")  
    preorder(root.left)  
    preorder(root.right)
```
## 二分图
**二分图** 定义：如果能将一个图的节点集合分割成两个独立的子集 `A` 和 `B` ，并使图中的每一条边的两个节点一个来自 `A` 集合，一个来自 `B` 集合，就将这个图称为 **二分图** 。
```python
# 返回图的二染色
# 如果是二分图，返回每个节点的颜色，用 1 和 2 表示两种颜色
# 如果不是二分图，返回空列表
# 时间复杂度 O(n+m)，n 是点数，m 是边数
def colorBipartite(n: int, edges: List[List[int]]) -> List[int]:
    # 建图（节点编号从 0 到 n-1）
    g = [[] for _ in range(n)]
    for x, y in edges:
        g[x].append(y)
        g[y].append(x)
    # colors[i] = 0 表示未访问节点 i
    # colors[i] = 1 表示节点 i 为红色
    # colors[i] = 2 表示节点 i 为蓝色
    colors = [0] * n
    def dfs(x: int, c: int) -> bool:
        colors[x] = c  # 节点 x 染成颜色 c
        for y in g[x]:
            # 邻居 y 的颜色与 x 的相同，说明不是二分图，返回 False
            # 或者继续递归，发现不是二分图，返回 False
            if colors[y] == c or \
               colors[y] == 0 and not dfs(y, 3 - c):  # 1 和 2 交替染色
                return False
        return True
    # 可能有多个连通块
    for i, c in enumerate(colors):
        if c == 0 and not dfs(i, 1):
            # 从节点 i 开始递归，发现 i 所在连通块不是二分图
            return []
    return colors
```
## 归并排序
```python
x=int(input())  #来自数字华容道
n=x**2
nums=[0]*n
row0=0
for i in range(x):
    alist=list(map(int,input().split()))
    for j in range(x):
        if alist[j]==0:
            row0=i
            rev=-(x*i+j)
        nums[x*i+j]=alist[j]
    alist=None
temp=[0]*n  #关键是准备一个空数组复制结果
def combine(left,mid,right):
    j=mid+1
    i=k=left
    while j<=right and i<=mid:
        if nums[j]<nums[i]:
            global rev
            rev+=(mid-i+1)
            temp[k]=nums[j]
            j+=1
        else:
            temp[k]=nums[i]
            i+=1
        k+=1
    while i<=mid:
        temp[k]=nums[i]
        i+=1
        k+=1
    while j<=right:
        temp[k]=nums[j]
        j+=1
        k+=1
    for k in range(left,right+1):
        nums[k]=temp[k]
    return
def merge(left,right):
    if left==right:
        return
    mid=(left+right)//2
    merge(left,mid)
    merge(mid+1,right)
    combine(left,mid,right)
    return
merge(0,len(nums)-1)
if x%2==1:
    if rev%2==0:
        print('yes')
    else:
        print('no')
else:
    if (rev+x-1-row0)%2==0:
        print('yes')
    else:
        print('no')
```
## 回溯
```python
MAPPING = "", "", "abc", "def", "ghi", "jkl", "mno", "pqrs", "tuv", "wxyz"
class Solution:#子集型回溯
    def letterCombinations(self, digits: str) -> List[str]:
        n = len(digits)
        if n == 0:
            return []
        ans = []
        path = [''] * n  # 注意 path 长度一开始就是 n，不是空列表
        def dfs(i: int) -> None:
            if i == n:
                ans.append(''.join(path))
                return
            for c in MAPPING[int(digits[i])]:
                path[i] = c  # 直接覆盖
                dfs(i + 1)
        dfs(0)
        return ans
```

```python
class Solution:  #组合型回溯，枚举选哪个
    def combine(self, n: int, k: int) -> List[List[int]]:
        ans = []
        path = []
        # 枚举选哪个：在 1 到 i 中选一个数，加到 path 末尾
        def dfs(i: int) -> None:
            d = k - len(path)  # 还要选 d 个数
            if d == 0:  # 选好了
                ans.append(path.copy())
                return
            # 枚举的数不能太小，否则后面没有数可以选
            for j in range(i, d - 1, -1):
                path.append(j)
                dfs(j - 1)
                path.pop()  # 恢复现场
        dfs(n)  # 从 n 开始倒着枚举
        return ans
class Solution:  #组合型回溯，选或不选
    def combine(self, n: int, k: int) -> List[List[int]]:
        ans = []
        path = []
        # 选或不选：讨论 i 是否加入 path
        def dfs(i: int) -> None:
            d = k - len(path)  # 还要选 d 个数
            if d == 0:  # 选好了
                ans.append(path.copy())
                return
            # 不选 i
            if i > d:
                dfs(i - 1)
            # 选 i
            path.append(i)
            dfs(i - 1)
            path.pop()  # 恢复现场
        dfs(n)  # 从 i=n 开始倒着枚举
        return ans
```
```python
def solveNQueens(self, n: int) -> List[List[str]]: #排列型回溯
	ans = []
	queens = [0] * n  # 皇后放在 (r,queens[r])
	col = [False] * n
	diag1 = [False] * (n * 2 - 1)
	diag2 = [False] * (n * 2 - 1)
	def dfs(r: int) -> None:
		if r == n:
			ans.append(['.' * c + 'Q' + '.' * (n - 1 - c) for c in queens])
			return
		# 在 (r,c) 放皇后
		for c, ok in enumerate(col):
			if not ok and not diag1[r + c] and not diag2[r - c]:  # 判断能否放皇后
				queens[r] = c  # 直接覆盖，无需恢复现场
				col[c] = diag1[r + c] = diag2[r - c] = True  # 皇后占用了 c 列和两条斜线
				dfs(r + 1)
				col[c] = diag1[r + c] = diag2[r - c] = False  # 恢复现场
	dfs(0)
	return ans
```
```python
def findSubsequences(nums):
    result = 0
    def backtrack(start, path):
        nonlocal result
        # 如果当前路径长度 >= 2，加入结果
        if len(path) >= 2:
            result+=1
        # 用集合来记录当前层已经使用过的数字，避免重复
        used = set()
        for i in range(start, len(nums)):
            # 如果当前数字已经被使用过，跳过（去重）
            if nums[i] in used:
                continue
            # 如果路径为空，或者当前数字 >= 路径最后一个数字（非递减）
            if not path or nums[i] >= path[-1]:
                used.add(nums[i])
                path.append(nums[i])
                backtrack(i + 1, path)
                path.pop()
    backtrack(0, [])
    return result
if __name__ == "__main__":
    nums = list(map(int, input().split()))
    print(findSubsequences(nums))
```
## 动态规划
```python
n=int(input())   #旅行售货商问题  状压dp  #优化后  
cost=[]  
for _ in range(n):  
    cost.append(list(map(int,input().split())))  
dp=[[float('inf')]*n for _ in range(1<<n)]  
dp[1][0]=0  
for mask in range(1,1<<n,2):  
    for u in range(n):  
        if dp[mask][u]==float('inf'):  
            continue  
        val=dp[mask][u]  
        for v in range(1,n):  
            if not (mask>>v)&1:  
                new_mask=mask|(1<<v)  
                new_val=val+cost[u][v]  
                if new_val<dp[new_mask][v]:  
                    dp[new_mask][v]=new_val  
ans=float('inf')  
final_mask=((1<<n)-1)  
for x in range(n):  
    ans=min(ans,dp[final_mask][x]+cost[x][0])  
print(ans)
```
```python
from bisect import bisect_left
class Solution:  #LIS
    def lengthOfLIS(self, nums: List[int]) -> int:  #贪心+二分 O(nlogn)
        g=[]
        for x in nums:
            i=bisect_left(g,x)
            if i==len(g):
                g.append(x)
            else:
                g[i]=x
        return len(g)
class Solution:   #二维LIS
    def maxEnvelopes(self, envelopes: List[List[int]]) -> int:
        envelopes.sort(key=lambda e:(e[0],-e[1]))   #第一个相同，第二个降序排
        n=len(envelopes)
        g=[]
        for _,x in envelopes:
            i=bisect_left(g,x)
            if i==len(g):
                g.append(x)
            else:
                g[i]=x
        return len(g)
```
```python
def rob(self, nums: List[int]) -> int: 打家劫舍
	f = [0] * (len(nums) + 2)
	for i, x in enumerate(nums):
		f[i + 2] = max(f[i + 1], f[i] + x)
	return f[-1]
def findTargetSumWays(self, nums: List[int], target: int) -> int:  0-1背包
	s = sum(nums) - abs(target)
	if s < 0 or s % 2:
		return 0
	m = s // 2  # 背包容量
	n = len(nums)
	f = [[0] * (m + 1) for _ in range(n + 1)]
	f[0][0] = 1
	for i, x in enumerate(nums):
		for c in range(m + 1):
			if c < x:
				f[i + 1][c] = f[i][c]  # 只能不选
			else:
				f[i + 1][c] = f[i][c] + f[i][c - x]  # 不选 + 选
	return f[n][m]
def coinChange(self, coins: List[int], amount: int) -> int:  完全背包
	n = len(coins)
	f = [[inf] * (amount + 1) for _ in range(n + 1)]
	f[0][0] = 0
	for i, x in enumerate(coins):
		for c in range(amount + 1):
			if c < x:
				f[i + 1][c] = f[i][c]
			else:
				f[i + 1][c] = min(f[i][c], f[i + 1][c - x] + 1)
	ans = f[n][amount]
	return ans if ans < inf else -1
def coinChange(self, coins: List[int], amount: int) -> int:  完全背包空间优化
	f = [0] + [inf] * amount
	for x in coins:
		for c in range(x, amount + 1):
			f[c] = min(f[c], f[c - x] + 1)
	ans = f[amount]
	return ans if ans < inf else -1
def longestCommonSubsequence(self, s: str, t: str) -> int: 最长公共子序列
	n, m = len(s), len(t)
	f = [[0] * (m + 1) for _ in range(n + 1)]
	for i, x in enumerate(s):
		for j, y in enumerate(t):
			f[i + 1][j + 1] = f[i][j] + 1 if x == y else \
							  max(f[i][j + 1], f[i + 1][j])
	return f[n][m]
def minDistance(self, s: str, t: str) -> int:
	f = list(range(len(t) + 1))
	for x in s:
		pre = f[0]
		f[0] += 1  # f[0] = i + 1
		for j, y in enumerate(t):
			tmp = f[j + 1]
			f[j + 1] = pre if x == y else min(f[j + 1], f[j], pre) + 1
			pre = tmp
	return f[-1]
def maxProfit(self, prices: List[int]) -> int:. 买卖股票的最佳时机 II
	f0, f1 = 0, -inf
	for p in prices:
		f0, f1 = max(f0, f1 + p), max(f1, f0 - p)
	return f0
def maxProfit(self, prices: List[int]) -> int:最佳买卖股票时机含冷冻期
	n = len(prices)
	f = [[0] * 2 for _ in range(n + 2)]
	f[1][1] = -inf
	for i, p in enumerate(prices):
		f[i + 2][0] = max(f[i + 1][0], f[i + 1][1] + p)
		f[i + 2][1] = max(f[i + 1][1], f[i][0] - p)
	return f[-1][0]
def maxProfit(self, k: int, prices: List[int]) -> int:  买卖股票的最佳时机 IV
	n = len(prices)
	f = [[[-inf] * 2 for _ in range(k + 2)] for _ in range(n + 1)]
	for j in range(1, k + 2):
		f[0][j][0] = 0
	for i, p in enumerate(prices):
		for j in range(1, k + 2):
			f[i + 1][j][0] = max(f[i][j][0], f[i][j][1] + p)
			f[i + 1][j][1] = max(f[i][j][1], f[i][j - 1][0] - p)
	return f[-1][-1][0]
def longestPalindromeSubseq(self, s: str) -> int:最长回文子序列
	n = len(s)
	f = [[0] * n for _ in range(n)]
	for i in range(n - 1, -1, -1):
		f[i][i] = 1
		for j in range(i + 1, n):
			if s[i] == s[j]:
				f[i][j] = f[i + 1][j - 1] + 2
			else:
				f[i][j] = max(f[i + 1][j], f[i][j - 1])
	return f[0][-1]
def minScoreTriangulation(self, v: List[int]) -> int:多边形三角剖分的最低得分
	n = len(v)
	f = [[0] * n for _ in range(n)]
	for i in range(n - 3, -1, -1):
		for j in range(i + 2, n):
			f[i][j] = min(f[i][k] + f[k][j] + v[i] * v[j] * v[k]
						  for k in range(i + 1, j))
	return f[0][-1]
class Solution:   二叉树的直径
    def diameterOfBinaryTree(self, root: Optional[TreeNode]) -> int:
        ans = 0
        def dfs(node: Optional[TreeNode]) -> int:
            if node is None:
                return 0
            l_len = dfs(node.left)
            r_len = dfs(node.right)
            nonlocal ans
            ans = max(ans, l_len + r_len)  # 两条链拼成路径
            return max(l_len, r_len) + 1
        dfs(root)
        return ans
class Solution:  二叉树中的最大路径和
    def maxPathSum(self, root: Optional[TreeNode]) -> int:
        ans = -inf
        def dfs(node: Optional[TreeNode]) -> int:
            if node is None:
                return 0  # 没有节点，和为 0
            l_val = dfs(node.left)  # 左子树最大链和
            r_val = dfs(node.right)  # 右子树最大链和
            nonlocal ans
            ans = max(ans, l_val + r_val + node.val)  # 两条链拼成路径
            return max(max(l_val, r_val) + node.val, 0)  # 当前子树最大链和（注意这里和 0 取最大值了）
        dfs(root)
        return ans
class Solution:   #链接直径中点
    def diameter(self, edges: List[List[int]]) -> int:
        g = [[] for _ in range(len(edges) + 1)]
        for x, y in edges:
            g[x].append(y)
            g[y].append(x)
        res = 0
        def dfs(x: int, fa: int) -> int:
            nonlocal res
            max_len = 0
            for y in g[x]:
                if y != fa:
                    sub_len = dfs(y, x) + 1
                    res = max(res, max_len + sub_len)
                    max_len = max(max_len, sub_len)
            return max_len
        dfs(0, -1)
        return res
    def minimumDiameterAfterMerge(self, edges1: List[List[int]], edges2: List[List[int]]) -> int:
        d1 = self.diameter(edges1)
        d2 = self.diameter(edges2)
        return max(d1, d2, (d1 + 1) // 2 + (d2 + 1) // 2 + 1)
class Solution:  #树上打家劫舍
    def rob(self, root: Optional[TreeNode]) -> int:
        def dfs(node: Optional[TreeNode]) -> Tuple[int, int]:
            if node is None:  # 递归边界
                return 0, 0  # 没有节点，怎么选都是 0
            l_rob, l_not_rob = dfs(node.left)  # 递归左子树
            r_rob, r_not_rob = dfs(node.right)  # 递归右子树
            rob = l_not_rob + r_not_rob + node.val  # 选
            not_rob = max(l_rob, l_not_rob) + max(r_rob, r_not_rob)  # 不选
            return rob, not_rob
        return max(dfs(root))  # 根节点选或不选的最大值
class Solution:  T 秒后青蛙的位置
    def frogPosition(self, n: int, edges: List[List[int]], t: int, target: int) -> float:
        g = [[] for _ in range(n + 1)]
        g[1] = [0]  # 减少额外判断的小技巧
        for x, y in edges:
            g[x].append(y)
            g[y].append(x)  # 建树
        ans = 0
        def dfs(x: int, fa: int, left_t: int, prod: int) -> True:
            # t 秒后必须在 target（恰好到达，或者 target 是叶子停在原地）
            if x == target and (left_t == 0 or len(g[x]) == 1):
                nonlocal ans
                ans = 1 / prod
                return True
            if x == target or left_t == 0: return False
            for y in g[x]:  # 遍历 x 的儿子 y
                if y != fa and dfs(y, x, left_t - 1, prod * (len(g[x]) - 1)):
                    return True  # 找到 target 就不再递归了
            return False  # 未找到 target
        dfs(1, 0, t, 1)
        return ans
class Solution:  #监控二叉树
    def minCameraCover(self, root: Optional[TreeNode]) -> int:
        def dfs(node):
            if node is None:
                return inf, 0, 0  # 空节点不能安装摄像头，也无需被监控到
            l_choose, l_by_fa, l_by_children = dfs(node.left)
            r_choose, r_by_fa, r_by_children = dfs(node.right)
            choose = min(l_choose, l_by_fa) + min(r_choose, r_by_fa) + 1
            by_fa = min(l_choose, l_by_children) + min(r_choose, r_by_children)
            by_children = min(l_choose + r_by_children, l_by_children + r_choose, l_choose + r_choose)
            return choose, by_fa, by_children
        choose, _, by_children = dfs(root)  # 根节点没有父节点
        return min(choose, by_children)
import sys  #保安站岗
sys.setrecursionlimit(2000)
n=int(sys.stdin.readline())
data=sys.stdin.read().strip().split('\n')
g=[[] for _ in range(n)]
vals=[0]*n
cnt=[0]*n
root=-1
for x in data:
    alist=list(map(int,x.split()))
    i=alist[0]-1
    vals[i]=alist[1]
    g[i]=list(map(lambda x:x-1,alist[3:]))
    for j in g[i]:
        cnt[j]+=1
for i in range(n):
    if cnt[i]==0:
        root=i
        break
def dfs(x,fa):
    choose,by_fa,by_son=vals[x],0,float('inf')   #边界条件注意，叶子节点和空节点终止不同
    son_choose,son_by_fa,son_by_son=[],[],[]
    for y in g[x]:
        if y==fa:   continue
        a,b,c=dfs(y,x)
        son_choose.append(a)
        son_by_fa.append(b)
        son_by_son.append(c)
    if not son_choose:
        return choose,by_fa,by_son
    choose=sum(min(a,b) for a,b in zip(son_choose,son_by_fa))+vals[x]
    by_fa=sum(min(a,c) for a,c in zip(son_choose,son_by_son))
    by_son=by_fa+max(0,min(a-c for a,c in zip(son_choose,son_by_son)))
    return choose,by_fa,by_son
choose,_,by_son=dfs(root,-1)
print(min(choose,by_son))
```
## 状压dp
```python
class Solution:    #s表示还可以选啥
    def countArrangement(self, n: int) -> int:
        @cache  # 缓存装饰器，避免重复计算 dfs 的结果（记忆化）
        def dfs(s: int) -> int:
            if s == 0:
                return 1
            res = 0
            i = s.bit_count()
            for j in range(1, n + 1):
                if s >> (j - 1) & 1 and (i % j == 0 or j % i == 0):
                    res += dfs(s ^ (1 << (j - 1)))
            return res
        return dfs((1 << n) - 1)
class Solution:
    def countArrangement(self, n: int) -> int:
        f = [0] * (1 << n)
        f[0] = 1
        for s in range(1, 1 << n):
            i = s.bit_count()
            for j in range(1, n + 1):
                if s >> (j - 1) & 1 and (i % j == 0 or j % i == 0):
                    f[s] += f[s ^ (1 << (j - 1))]
        return f[-1]
class Solution:  #排列型，相邻相关
    def specialPerm(self, nums: List[int]) -> int:
        @cache
        def dfs(s: int, i: int) -> int:
            if s == 0:
                return 1  # 找到一个特别排列
            res = 0
            pre = nums[i]
            for j, x in enumerate(nums):
                if s >> j & 1 and (pre % x == 0 or x % pre == 0):
                    res += dfs(s ^ (1 << j), j)
            return res
        n = len(nums)
        u = (1 << n) - 1
        return sum(dfs(u ^ (1 << i), i) for i in range(n)) % 1_000_000_007
class Solution:
    def specialPerm(self, nums: List[int]) -> int:
        n = len(nums)
        u = (1 << n) - 1
        f = [[0] * n for _ in range(u)]
        f[0] = [1] * n
        for s in range(1, u):
            for i, pre in enumerate(nums):
                if s >> i & 1:
                    continue
                for j, x in enumerate(nums):
                    if s >> j & 1 and (pre % x == 0 or x % pre == 0):
                        f[s][i] += f[s ^ (1 << j)][j]
        return sum(f[u ^ (1 << i)][i] for i in range(n)) % 1_000_000_007
class Solution:  子集状压dp
    def distributeCookies(self, cookies: List[int], k: int) -> int:
        m = 1 << len(cookies)
        SUM = [0] * m
        for i, v in enumerate(cookies):
            bit = 1 << i
            for j in range(bit):
                SUM[bit | j] = SUM[j] + v

        f = SUM.copy()
        for _ in range(1, k):
            for j in range(m - 1, 0, -1):
                s = j
                while s:
                    v = f[j ^ s]
                    if SUM[s] > v: v = SUM[s]  # 不要用 max 和 min，那样会有额外的函数调用开销
                    if v < f[j]: f[j] = v
                    s = (s - 1) & j
        return f[-1]
阅读提示：请注意「前 i 个」和「第 i 个」的区别，前者用来表示状态，后者用于参与状态转移。
定义 f[i][j] 表示前 i 个孩子分配的饼干集合为 j 时，前 i 个孩子的不公平程度的最小值。
下文中 j∖s 表示从集合 j 中去掉集合 s 的元素后，剩余元素组成的集合。
考虑给第 i 个孩子分配的饼干集合为 s，设集合 s 的元素和为 sum[s]，分类讨论：
如果 sum[s]>f[i−1][j∖s]，说明给第 i 个孩子分配的饼干比前面的孩子多，不公平程度变为 sum[s]；
如果 sum[s]≤f[i−1][j∖s]，说明给第 i 个孩子分配的饼干没有比前面的孩子多，不公平程度不变，仍为 f[i−1][j∖s]。
因此，给第 i 个孩子分配饼干集合 s 后，前 i 个孩子的不公平程度为
max(f[i−1][j∖s],sum[s])
枚举 j 的所有子集 s，则有
f[i][j]= s⊆jmin​max(f[i−1][j∖s],sum[s])
代码实现时，我们可以用一个二进制数来表示集合，其第 i 位为 1 表示分配了第 i 块饼干，为 0 表示未分配第 i 块饼干。
此外通过倒序枚举 j，f 的第一个维度可以省略。sum 也可以通过预处理得到。
```
## DFS
```python
def solve(n: int, edges: List[List[int]]) -> List[int]:
    # 节点编号从 0 到 n-1
    g = [[] for _ in range(n)]
    for x, y in edges:
        g[x].append(y)
        g[y].append(x)  # 无向图
    vis = [False] * n
    def dfs(x: int) -> int:
        vis[x] = True  # 避免重复访问节点
        size = 1
        for y in g[x]:
            if not vis[y]:
                size += dfs(y)
        return size
    # 计算每个连通块的大小
    ans = []
    for i, b in enumerate(vis):
        if not b:  # i 没有访问过
            size = dfs(i)
            ans.append(size)
    return ans
class Solution:  #dfs染色法判环
    def canFinish(self, numCourses: int, prerequisites: List[List[int]]) -> bool:
        g = [[] for _ in range(numCourses)]
        for a, b in prerequisites:
            g[b].append(a)

        colors = [0] * numCourses
        # 返回 True 表示找到了环
        def dfs(x: int) -> bool:
            colors[x] = 1  # x 正在访问中
            for y in g[x]:
                # 情况一：colors[y] == 1，表示发生循环依赖，找到了环
                # 情况二：colors[y] == 0，没有访问过 y，继续递归 y 获取信息
                # 情况三：colors[y] == 2，重复访问 y 只会重蹈覆辙，和之前一样无法找到环，跳过
                if colors[y] == 1 or colors[y] == 0 and dfs(y):
                    return True  # 找到了环
            colors[x] = 2  # x 完全访问完毕，从 x 出发无法找到环
            return False  # 没有找到环

        for i, c in enumerate(colors):
            if c == 0 and dfs(i):
                return False  # 有环
        return True  # 没有环
```
```python
def minimumHammingDistance(self, source: List[int], target: List[int], allowedSwaps: List[List[int]]) -> int:  
	n = len(source)  #最小汉明距离
	g = [[] for _ in range(n)]  #在 ai​ 和 bi​ 之间连边，得到一个无向图 G。
	for i, j in allowedSwaps:  #G 的同一个连通块中的节点（下标）可以随意交换。
		g[i].append(j)  # 建图  
		g[j].append(i)  
	def dfs(x: int) -> None:  
		vis[x] = True  # 避免重复访问  
		# 抵消相同的元素，最终剩下 source 和 target 各自多出来的元素（对称差）  
		diff[source[x]] += 1  
		diff[target[x]] -= 1  
		for y in g[x]:  
			if not vis[y]:  
				dfs(y)  
	vis = [False] * n  
	ans = 0  
	for x in range(n):  
		if not vis[x]:  
			diff = defaultdict(int)  
			dfs(x)  
			ans += sum(map(abs, diff.values()))  
	return ans // 2  # 有 ans // 2 对多出来的元素
```
## BFS
```python
import sys  #词梯
from collections import deque
words=sys.stdin.read().split()
n=int(words[0])#枚举只差一个字母的所有可能，再看是否在集合中，而不是一个个对比。
start=words[-2]
end=words[-1]
words=words[1:n+1]
s1='abcdefghijklmnopqrstuvwxyz'
s2='abcdefghijklmnopqrstuvwxyz'.upper()
if end[1] in s1:
    s=s1
else:
    s=s2
def build_graph(words):       #建图
    aset=set(words)
    g={word:[] for word in words}
    for word in words:
        word_list=list(word)
        for i in range(4):
            for c in s:
                if c==word[i]:
                    continue
                word_list[i]=c
                modify_word=''.join(word_list)
                if modify_word in aset:
                    g[word].append(modify_word)
            word_list = list(word)
    return  g
g=build_graph(words)
visited={word:False for word in words}
q=deque([[start]])
while q:
    curpath=q.popleft()
    if curpath[-1]==end:
        print(' '.join(curpath))
        break
    for nxt in g[curpath[-1]]:
        if not visited[nxt]:
            visited[nxt]=True
            curpath.append(nxt)
            q.append(curpath.copy())   #固定队列中的路径！！
            curpath.pop()
else:
    print('NO')
```
多源bfs
对一个岛中所有格子多次bfs会超时。多源bfs，从其中一个岛的所有格子同时出发。用visited矩阵标记访问状态，这样每个格子最多被访问4次，能做到O(n^2)的时间复杂度。
```python
from collections import deque
data=sys.stdin.read().strip().split('\n')
n=int(data[0])
g=[0]*n
for i,x in enumerate(data[1:]):
    g[i]=list(map(int,list(x)))
dir=[(1,0),(-1,0),(0,1),(0,-1)]
visited=[[False]*n for _ in range(n)]
island2=deque([])
def find():
    for i in range(n):
        for j in range(n):
            if g[i][j]==0:
                continue
            if g[i][j]==1:
                g[i][j]=2
                island2.append((i,j,0))
                visited[i][j]=True
                q=deque([(i,j)])
                while q:
                    i,j=q.popleft()
                    for addi,addj in dir:
                        x,y=i+addi,j+addj
                        if 0<=x<=n-1 and 0<=y<=n-1 and g[x][y]==1:
                            g[x][y]=2
                            island2.append((x,y,0))
                            visited[x][y]=True
                            q.append((x,y))
                while island2:
                    i,j,step=island2.popleft()
                    if g[i][j]==1:
                        return step-1
                    for addi, addj in dir:
                        x, y = i + addi, j + addj
                        if 0 <= x <= n - 1 and 0 <= y <= n - 1 and not visited[x][y]:  #标记状态，这样时间复杂度就能到n**2
                            visited[x][y]=True
                            island2.append((x, y,step+1))
print(find())
```
## 懒删除堆
```python
class LazyHeap:
    def __init__(self):
        self.heap = []  # 最小堆（最大堆可以把数字取反或重载 __lt__）
        self.remove_cnt = defaultdict(int)  # 每个元素剩余需要删除的次数
        self.size = 0  # 堆的实际大小
    # 删除
    def remove(self, x: Any) -> None:
        self.remove_cnt[x] += 1  # 懒删除
        self.size -= 1
    # 正式执行删除操作
    def _apply_remove(self) -> None:
        while self.heap and self.remove_cnt[self.heap[0]] > 0:
            self.remove_cnt[self.heap[0]] -= 1
            heappop(self.heap)
    # 查看堆顶
    def top(self) -> Any:
        self._apply_remove()
        return self.heap[0]  # 真正的堆顶
    # 出堆
    def pop(self) -> Any:
        self._apply_remove()
        self.size -= 1
        return heappop(self.heap)
    # 入堆
    def push(self, x: Any) -> None:
        if self.remove_cnt[x] > 0:
            self.remove_cnt[x] -= 1  # 抵消之前的删除
        else:
            heappush(self.heap, x)
        self.size += 1
```