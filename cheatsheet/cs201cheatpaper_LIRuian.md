# 一些注意事项
## 增加递归深度
```python
import sys
sys.setrecursionlimit(100000)    # 设置最大递归深度
```
## 关于sort
```python
l=[
{'name': 'Alice', 'score': 85, 'id': 3},
{'name': 'Bob', 'score': 92, 'id': 1},
{'name': 'Charlie', 'score': 85, 'id': 2},
{'name': 'Diana', 'score': 78, 'id': 4}
]

l.sort(key=lambda x:(-x[score],x[id]))
l1=sorted(l,key=lambda x:(-x[score],x[id]))
```
## 判断是不是数字,字母
```python
a=1,b="a"
if a.isdigit():# 要求a纯数字
    print("YES")
if b.isalpha():# 要求b纯字母
    print("YES")
```
## 大小写互换和转大写小写
```python
s = "hElLo WoRlD"

print(s.upper())         # HELLO WORLD
print(s.lower())         # hello world
print(s.swapcase())      # HeLLo wOrLd
print(s.capitalize())    # Hello world
print(s.title())         # Hello World

# 判断（跳过非英文字符）
print(s.islower())       # False
print("abc".islower())   # True
print(s.isupper())       # False
```
## rstrip,lstrip,strip
```python
a="   abc   "
b=a.rstrip();c=a.lstrip();d=a.strip()
# 括号里也可以指定要删除什么字符，如a.strip("xy")就是删除字符串a左右两端的x和y，第一次遇到不在"xy"中的字符就停止。
```
## replace
```python
s = "hello world hello"
print(s.replace("hello", "hi"))      # "hi world hi"  全部替换
print(s.replace("hello", "hi", 1))   # "hi world hello"  只替换第一个
print(s)                             # 原字符串不变："hello world hello"
s = "I like cats and dogs."
s = s.replace("cats", "bunnies").replace("dogs", "birds")
print(s)   # "I like bunnies and birds."
```
## find
```python
s = "hello world"
print(s.find("world"))   # 6  （'w'的索引）
print(s.find("o"))       # 4  （第一个'o'的位置）
print(s.find("xyz"))     # -1 （不存在）
s = "hello world"
# 从索引5开始找"o"
print(s.find("o", 5))    # 7  （"world"中的'o'）
# 在索引0到5之间找"o"
print(s.find("o", 0, 5)) # 4  （"hello"中的'o'）
s = "hello world"
print(s.find("o"))      # 4
print(s.index("o"))     # 4
print(s.rfind("o"))     # 7（最后一个o）
```
## 输出小数
```python
s=1/7
print("{:.2f}".format(s))
print(f"{s:.2f}")
# 这里是两位小数
```
## count
```python
s=" a a a "
print(s.count(" a "))
# 输出2，因为第一个" a "数走后变成了"a a "
```
## 矩阵转置
```python
matrix = [[1,2,3],[4,5,6]]
transposed = list(zip(*matrix))   # 得到元组列表
# 若需要列表内是列表而非元组，再转换一下
transposed = [list(row) for row in zip(*matrix)]
print(transposed)  # [[1,4],[2,5],[3,6]]
```
## enumerate
enumerate(iterable, start=0)
iterable：任何可迭代对象，比如列表、元组、字符串等。
start：索引的起始值，默认是0。
返回值：它返回一个枚举对象，每次迭代生成一个元组 (index, value)。
```python
a=["apple","banana","orange"]
b=list(enumerate(a,1))
# 或者
for i,j in enumerate(a,1):
    print(i,j)
```
## 关于精度
int(a/b)向0取整，a//b向下取整，a//b中a,b有一个是浮点数，就会输出浮点数（但是数值还是整数），并且int会出现浮点数精度问题。另外，整数太大再进行乘除运算也可能会出现精度的问题。另外，向下取整math.floor()，向上取整math.ceil()。
## 浮点数相等性的判断
math.isclose(a, b,  rel_tol=1e-09, abs_tol=0.0)
rel_tol相对容忍度
abs_tol绝对容忍度
要求abs(a-b)<=abs_tol或rel_tol*max(a,b)
## 列表元素删除
```python
fruits = ['apple', 'banana', 'cherry', 'banana', 'date']
# del
del fruits[1]          # 删除索引1（第二个元素）
print(fruits)          # ['apple', 'cherry', 'banana', 'date']

del fruits[-2:]        # 删除倒数两个元素
print(fruits)          # ['apple', 'cherry']
# pop
removed = fruits.pop(2)   # 删除索引2的元素
print(removed)            # 'banana'
print(fruits)             # ['apple', 'cherry', 'date']

last = fruits.pop()       # 不传索引，删除最后一个
print(last)               # 'date'
print(fruits)             # ['apple', 'cherry']
# remove
fruits = ['apple', 'banana', 'cherry', 'banana']
fruits.remove('banana')   # 只删第一个 'banana'
print(fruits)             # ['apple', 'cherry', 'banana']
# clear
fruits.clear()
print(fruits)   # []
# 按索引删除的陷阱
nums = [1, 2, 3, 4]
for i in range(len(nums)):
    if nums[i] % 2 == 0:
        del nums[i]   # 索引会错乱！
# 正确做法是从后往前删，或者构建新的列表
```
## 集合操作
```python
# ==================== 🧙 集合操作大全 ====================

# 一、创建集合
s_empty = set()                      # 空集合
fruits = {"apple", "banana", "cherry"}  # 直接创建
nums = set([1, 2, 3, 2, 1])         # 从列表转换（自动去重） -> {1,2,3}

# 二、添加与删除元素
s = {1, 2, 3}
s.add(4)            # {1,2,3,4}       添加元素
s.add(2)            # 不变，因为2已存在

s.remove(2)         # {1,3,4}         删除，元素不存在会报错
s.discard(5)        # 不报错，即使5不存在

popped = s.pop()    # 随机弹出一个元素，空集合报错
print(f"弹出: {popped}")

s.clear()           # 清空集合 -> set()

# 三、数学运算（返回新集合，原集合不变）
a = {1, 2, 3}
b = {2, 3, 4}

union = a | b                       # 并集 {1,2,3,4}
union_method = a.union(b)

inter = a & b                       # 交集 {2,3}
inter_method = a.intersection(b)

diff = a - b                        # 差集 {1}
diff_method = a.difference(b)

sym_diff = a ^ b                    # 对称差 {1,4}
sym_diff_method = a.symmetric_difference(b)

# 四、原地修改运算（修改原集合）
a.update({3, 5})                     # 并集更新 -> a变为 {1,2,3,5}
a &= {2, 3}                          # 交集更新 -> {2,3}
a -= {2}                             # 差集更新 -> {3}
a ^= {3, 4}                          # 对称差更新 -> {4}

# 五、集合比较
x = {1, 2}
y = {1, 2, 3}
print(x <= y)            # True，子集
print(x < y)             # True，真子集
print(y >= x)            # True，超集
print(y > x)             # True，真超集
print(x.isdisjoint({4,5}))  # True，无交集

# 六、其他常用操作
s = {1, 2, 3}
print(len(s))            # 3，元素个数
print(2 in s)            # True，成员检查

# 复制
s_copy = s.copy()
s_copy2 = set(s)

# 遍历（注意无序）
for item in s:
    print(item)

# 集合推导式
squares = {x**2 for x in range(5)}   # {0,1,4,9,16}

# 七、实用技巧：列表去重（但顺序会变）
my_list = [3, 1, 2, 1, 3]
unique = list(set(my_list))  # [1,2,3] 顺序不保证
```
# 前缀和
## accumulate
```python
l=[1,2,3,4]
pre=[0]+accumulate(l)
```
也可以前缀最大最小和前缀乘积
```python
l=[1,2,3,4]
pre1=accumulate(l,max)
pre2=accumulate(l,operator.mul)
```
## 差分数组
在需要改变的区间的起点和终点加减，最后用前缀和即可得到我们想要的数组，不支持边查询边修改。
```python
n = 10
a = [0]*(n+2)      # 原数组，1-indexed
d = [0]*(n+2)      # 差分数组

def add(l, r, k):
    d[l] += k
    d[r+1] -= k

# 进行若干次区间加
add(2,5,3)
add(4,8,2)

# 构建最终数组
for i in range(1, n+1):
    d[i] += d[i-1]      # d 变成前缀和，此时 d[i] 就是 a[i]
    a[i] = d[i]

print(a[1:])   # 结果
```
# 位运算
## 基本符号
```python
# 定义两个示例整数
a = 5      # 二进制 0101
b = 3      # 二进制 0011

print(f"a = {a} (二进制: {a:04b})")
print(f"b = {b} (二进制: {b:04b})")
print()

# 1. 按位与 &
print(f"a & b = {a & b} (二进制: {a & b:04b})   # 同为1才为1")

# 2. 按位或 |
print(f"a | b = {a | b} (二进制: {a | b:04b})   # 至少一个1为1")

# 3. 按位异或 ^
print(f"a ^ b = {a ^ b} (二进制: {a ^ b:04b})   # 不同则为1")

# 4. 按位取反 ~
print(f"~a = {~a}   # 公式: -a-1 = -6")
print(f"~a 的低4位: {(~a) & 0b1111:04b}   # 只看低4位效果是取反")

# 5. 左移 <<
print(f"a << 1 = {a << 1} (二进制: {a << 1:08b})   # 相当于乘以2")

# 6. 右移 >>
print(f"a >> 1 = {a >> 1} (二进制: {a >> 1:04b})   # 相当于整除2")

# 7. 二进制长度
print(a.bit_length())

# 8. 二进制一的个数
print(a.bit_count())
```
## 一些位运算技巧
```python
# ==================== 位运算技巧 6~11 综合演示 ====================

# ---------- 技巧6：获取最低位的1（lowbit） ----------
x = 12  # 1100
lowbit = x & -x
print(f"x = {x} (二进制: {bin(x)})")
print(f"lowbit = x & -x = {lowbit} (二进制: {bin(lowbit)})")

# ---------- 技巧7：判断是否是2的幂 ----------
def is_power_of_two(x):
    return x > 0 and (x & (x - 1)) == 0

for v in [16, 18, 1, 0]:
    print(f"{v} 是2的幂吗？ {is_power_of_two(v)}")

# ---------- 技巧8：清除最低位的1 ----------
x = 0b10110  # 22
x = x & (x - 1)
print(f"清除最低位1后: {bin(x)} ({x})")

# ---------- 技巧9：枚举子集（状态压缩常用） ----------
mask = 0b1101  # 13
sub = mask
while sub:
    print(f"子集: {bin(sub)} ({sub})")
    sub = (sub - 1) & mask
print("(空集可单独处理)")

# ---------- 技巧10：取某个二进制位（第k位，从0开始） ----------
x = 0b10110  # 22
k = 2
bit = (x >> k) & 1
print(f"x = {bin(x)} ({x}), 第 {k} 位的值 = {bit}")

# ---------- 技巧11：将第k位置1 / 置0 / 翻转 ----------
x = 0b1010  # 10
k = 1
print(f"原数: {bin(x)} ({x})")
x |= (1 << k)
print(f"置1第{k}位后: {bin(x)} ({x})")
x &= ~(1 << 2)
print(f"置0第2位后: {bin(x)} ({x})")
x ^= (1 << 0)
print(f"翻转第0位后: {bin(x)} ({x})")
```
## 状压DP
```python
def tsp_dp(n, dist):
    """
    n: 城市数量（0..n-1）
    dist: 二维列表，dist[i][j] 表示 i 到 j 的距离
    返回: (最短距离, 最优路径列表) 若没有回路则返回 (None, [])
    """
    INF = float('inf')
    total_states = 1 << n
    dp = [[INF] * n for _ in range(total_states)]
    # 记录前驱，用于重建路径: pre[mask][i] = (prev_mask, prev_node)
    pre = [[(-1, -1)] * n for _ in range(total_states)]

    dp[1][0] = 0  # 起点城市0

    for mask in range(1, total_states):
        # 起点未在mask中则跳过
        if not (mask & 1):
            continue
        for i in range(n):
            if not (mask & (1 << i)):
                continue
            if dp[mask][i] == INF:
                continue
            # 尝试去下一个城市 j
            for j in range(n):
                if mask & (1 << j):
                    continue  # 已经访问过
                new_mask = mask | (1 << j)
                new_dist = dp[mask][i] + dist[i][j]
                if new_dist < dp[new_mask][j]:
                    dp[new_mask][j] = new_dist
                    pre[new_mask][j] = (mask, i)

    full_mask = total_states - 1  # 所有城市都访问过
    ans = INF
    last_city = -1
    for i in range(1, n):
        if dp[full_mask][i] == INF:
            continue
        total = dp[full_mask][i] + dist[i][0]  # 回到起点
        if total < ans:
            ans = total
            last_city = i

    if ans == INF:
        return None, []

    # 重建路径
    path = []
    mask = full_mask
    city = last_city
    while mask != -1:
        path.append(city)
        mask, city = pre[mask][city]
    path.append(0)  # 路径现在是倒序，最后加上起点
    path.reverse()
    return ans, path
```
# 归并排序
```python
def merge_sort_and_count(arr):
    """
    归并排序，同时返回排序后的数组和逆序数
    """
    if len(arr) <= 1:
        return arr, 0
    
    mid = len(arr) // 2
    left, inv_left = merge_sort_and_count(arr[:mid])
    right, inv_right = merge_sort_and_count(arr[mid:])
    
    merged, inv_merge = merge_and_count(left, right)
    
    return merged, inv_left + inv_right + inv_merge

def merge_and_count(left, right):
    """
    合并两个有序数组，并统计合并过程中产生的逆序对
    """
    result = []
    i = j = 0
    inv_count = 0
    
    while i < len(left) and j < len(right):
        if left[i] <= right[j]:
            result.append(left[i])
            i += 1
        else:
            # 当 right[j] < left[i] 时，left[i:] 的所有元素都大于 right[j]
            # 因此产生 len(left) - i 个逆序对
            result.append(right[j])
            inv_count += len(left) - i
            j += 1
    
    # 剩余元素直接追加
    result.extend(left[i:])
    result.extend(right[j:])
    
    return result, inv_count
```
# 快速排序
```python
def quick_sort(arr, left, right):
    """
    对 arr[left:right+1] 进行快速排序
    """
    if left < right:
        # 分区，得到基准的最终位置
        pivot_index = partition(arr, left, right)
        # 递归排序左右两部分
        quick_sort(arr, left, pivot_index - 1)
        quick_sort(arr, pivot_index + 1, right)

def partition(arr, left, right):
    """
    以 arr[right] 为基准，将数组分成两部分，
    返回基准最终所在的位置
    """
    pivot = arr[right]
    i = left - 1          # i 指向小于基准区域的最后一个位置
    for j in range(left, right):
        if arr[j] <= pivot:
            i += 1
            arr[i], arr[j] = arr[j], arr[i]
    # 把基准放到正确位置
    arr[i+1], arr[right] = arr[right], arr[i+1]
    return i + 1
```
# 调度场算法
```python
def process(s):
    n=len(s)
    l=[]
    i=0
    while i<n:
        if s[i].isdigit():
            a=s[i]
            i+=1
            while i<n and (s[i].isdigit() or s[i]=="."):
                a+=s[i]
                i+=1
            l.append(a)
        else:
            l.append(s[i])
            i+=1
    return l
pre={"+":1,"-":1,"*":2,"/":2}
for _ in range(int(input())):
    s=input()
    l=process(s)
    stack = []
    postfix = []
    for i in l:
        if i in "+-*/":
            while postfix and postfix[-1] in '+-*/' and pre[i] <= pre[postfix[-1]]:
                stack.append(postfix.pop())
            postfix.append(i)
        elif i=="(":
            postfix.append(i)
        elif i==")":
            while postfix[-1]!="(":
                stack.append(postfix.pop())
            postfix.pop()
        else:
            stack.append(i)
    while postfix:
        stack.append(postfix.pop())
    print(" ".join(str(i) for i in stack))
```
# 二分查找
随便找一个值为target的
```python
def binary_search(arr, target):
    left, right = 0, len(arr) - 1
    while left <= right:
        mid = (left + right) // 2
        if arr[mid] == target:
            return mid          # 找到，返回索引
        elif arr[mid] < target:
            left = mid + 1
        else:
            right = mid - 1
    return -1                   # 未找到
```
找左边第一个值为target的（用左闭右开区间）
```python
def left_bound(arr, target):
    left, right = 0, len(arr)
    while left < right:
        mid = (left + right) // 2
        if arr[mid] < target:
            left = mid + 1
        else:
            right = mid
    return left   # 如果 arr[left] == target 则找到，否则是插入点
```
找右边第一个值为target的（用左闭右开区间）
```python
def right_bound(arr, target):
    left, right = 0, len(arr)
    while left < right:
        mid = (left + right) // 2
        if arr[mid] <= target:
            left = mid + 1
        else:
            right = mid
    return left - 1   # 最后一个 ≤ target 的位置
```
# 并查集
```python
def make_set(n):
    """初始化并查集，返回 (parent, rank) 元组"""
    parent = list(range(n + 1))   # 1-indexed，如果0-indexed可调整
    rank = [0] * (n + 1)
    return parent, rank

def find(parent, x):
    """查找根节点，带路径压缩（递归写法）"""
    if parent[x] != x:
        parent[x] = find(parent, parent[x])
    return parent[x]

def union(parent, rank, x, y):
    """合并两个元素所在的集合，按秩合并，返回是否合并成功"""
    rx = find(parent, x)
    ry = find(parent, y)
    if rx == ry:
        return False
    if rank[rx] < rank[ry]:
        rx,ry=ry,rx
    parent[ry] = rx
    if rank[rx] == rank[ry]:
        rank[rx] += 1
    return True
```
# 图
## 染色法
一开始把所有点设置为白色，第一次访问时变成灰色，完成访问后变成黑色，灰色点代表可能还有白色点与其连接，黑色点则没有了。可以用color字典表示。
其他应用：如判断奇数环：相邻点染相反颜色，如果有相同颜色相邻，证明有奇数环
## 判环方法
有向图：拓扑排序，dfs用颜色表示点的状态
无向图：dfs看可访问的非visit对象是不是parent,并查集
```python
def has_cycle_undirected(graph):
    visited = set()
    def dfs(node, parent):
        visited.add(node)
        for neighbor in graph[node]:
            if neighbor not in visited:
                if dfs(neighbor, node):
                    return True
            elif neighbor != parent:
                return True
        return False
    for node in graph:
        if node not in visited:
            if dfs(node, -1):
                return True
    return False
```
```python
class UnionFind:
    def __init__(self, n):
        self.parent = list(range(n))
    def find(self, x):
        if self.parent[x] != x:
            self.parent[x] = self.find(self.parent[x]) # 路径压缩
        return self.parent[x]
    def union(self, x, y):
        root_x, root_y = self.find(x), self.find(y)
        if root_x == root_y:
            return False # 同⼀集合，成环
        self.parent[root_y] = root_x
        return True
def has_cycle_union_find(n, edges):
    uf = UnionFind(n)
    for u, v in edges:
        if not uf.union(u, v):
            return True
    return False
```
## Warnsdorff算法
骑士周游问题，注意可行路径是实时更新的而非一开始的总可行路径。
```python
n=int(input())
m=n**2
x,y=map(int,input().split())
dire=[(1,2),(-1,2),(1,-2),(-1,-2),(2,1),(2,-1),(-2,1),(-2,-1)]
# 构建图
d={}
for i in range(m):
    d[i]=[]
    x=i%n
    y=i//n
    for dx,dy in dire:
        nx,ny=x+dx,y+dy
        if 0<=nx<n and 0<=ny<n:
            d[i].append(nx+n*ny)
# dfs
t=x+n*y
visit=[0]*m
visit[t]=1
ans=False
def get_degree(v):
    cnt=0
    for neighbor in d[v]:
        if not visit[neighbor]:
            cnt+=1
    return cnt
def dfs(t,k):
    global ans
    if k==m:
        ans=True
        return
    candidates=[nxt for nxt in d[t] if not visit[nxt]]
    candidates.sort(key=lambda v:get_degree(v))
    for nxt in candidates:
        visit[nxt]=1
        dfs(nxt,k+1)
        if ans:
            return
        visit[nxt] = 0
dfs(t,1)
if ans:
    print("success")
else:
    print("fail")
```
## 最短路径算法
### Dijkstra算法：⽤于找到两个顶点之间的最短路径。
### Bellman-Ford算法：⽤于处理带有负权边的图的最短路径问题。
```python
def bellman_ford(n, edges, start):
    """
    :param n: 节点数 (0 ~ n-1)
    :param edges: 边列表 [(u, v, w), ...] 表示 u→v 权重 w
    :param start: 起点
    :return: (dist, has_negative_cycle)
    """
    dist = [float('inf')] * n
    dist[start] = 0

    # 进行 n-1 轮松弛
    for _ in range(n - 1):
        updated = False
        for u, v, w in edges:
            if dist[u] != float('inf') and dist[u] + w < dist[v]:
                dist[v] = dist[u] + w
                updated = True
        if not updated:  # 没有更新就可以提前收工喵！
            break

    # 第 n 轮：检测负环
    has_cycle = False
    for u, v, w in edges:
        if dist[u] != float('inf') and dist[u] + w < dist[v]:
            has_cycle = True
            break

    return dist, has_cycle
# 队列版，即SPFA
from collections import deque

def spfa(n, graph, start):
    """
    :param n: 节点数 (0 ~ n-1)
    :param graph: 邻接表 graph[u] = [(v, w), ...]
    :param start: 起点
    :return: (dist, has_negative_cycle)
    """
    dist = [float('inf')] * n
    dist[start] = 0
    
    in_queue = [False] * n
    cnt = [0] * n          # 记录每个节点入队次数
    q = deque()
    
    q.append(start)
    in_queue[start] = True
    cnt[start] = 1
    
    while q:
        u = q.popleft()
        in_queue[u] = False
        
        for v, w in graph[u]:
            if dist[u] + w < dist[v]:
                dist[v] = dist[u] + w
                if not in_queue[v]:
                    q.append(v)
                    in_queue[v] = True
                    cnt[v] += 1
                    if cnt[v] >= n:   # 入队超过 n-1 次 => 负环
                        return dist, True
    
    return dist, False
```
### Floyd-Warshall算法：⽤于找到图中所有顶点之间的最短路径。
初始化
```python
dist=[[float("inf")]*n for i in range(n)]
for v,w in graph[u]:
    dist[u][v]=w
```
主回路
```python
for k in range(n):
    for i in range(n):
        for j in range(n):
            if dist[i][k] + dist[k][j] < dist[i][j]:
                dist[i][j] = dist[i][k] + dist[k][j]
```
## 最小生成树算法
### Prim算法：⽤于找到连接所有顶点的最⼩生成树。
```python
class Solution:
    def minCostConnectPoints(self, points: List[List[int]]) -> int:
        ans=0
        n=len(points)
        graph=defaultdict(list)
        for i in range(n):
            for j in range(n):
                graph[i].append((abs(points[i][0]-points[j][0])+abs(points[i][1]-points[j][1]),j))
        vis=[0]*n
        vis[0]=1
        heap=graph[0]
        heapq.heapify(heap)
        t=1
        while heap and t<n:
            dis,x=heapq.heappop(heap)
            if vis[x]==0:
                vis[x]=1
                ans+=dis
                t+=1
                for nd,nx in graph[x]:
                    if vis[nx]==0:
                        heapq.heappush(heap,(nd,nx))
        return ans
```
### Kruskal算法 / 并查集：⽤于找到连接所有顶点的最⼩⽣成树，适⽤于边集合已经给定的情况。
### Boruvka算法
```python
import sys

def boruvka(n, edges):
    """
    n: 顶点数 (0~n-1)
    edges: 边列表，每条边为 (u, v, w)
    return: 最小生成树的总权值（若图连通），否则返回 -1
    """
    parent = list(range(n))
    rank = [0] * n

    def find(x):
        while parent[x] != x:
            parent[x] = parent[parent[x]]
            x = parent[x]
        return x

    def union(x, y):
        rx, ry = find(x), find(y)
        if rx == ry:
            return False
        if rank[rx] < rank[ry]:
            parent[rx] = ry
        elif rank[rx] > rank[ry]:
            parent[ry] = rx
        else:
            parent[ry] = rx
            rank[rx] += 1
        return True

    total_weight = 0
    comp_cnt = n

    # 当连通分量多于 1 个时继续
    while comp_cnt > 1:
        # 存储每个分量的最轻出边 (权值, 端点u, 端点v)
        cheapest = [(-1, -1, float('inf'))] * n   # (from, to, weight)

        # 遍历每条边，更新其两个端点所在分量的最轻出边
        for u, v, w in edges:
            ru, rv = find(u), find(v)
            if ru == rv:
                continue
            # 更新 ru 分量的最轻出边
            if w < cheapest[ru][2]:
                cheapest[ru] = (u, v, w)
            # 更新 rv 分量的最轻出边
            if w < cheapest[rv][2]:
                cheapest[rv] = (u, v, w)

        # 合并本轮选出的边
        merged = False
        for i in range(n):
            # 只处理有效分量（parent[i] == i 才是代表元）
            if parent[i] != i:
                continue
            u, v, w = cheapest[i]
            if u == -1:    # 该分量没有向外连边（图不连通）
                return -1
            if union(u, v):
                total_weight += w
                comp_cnt -= 1
                merged = True
        if not merged:     # 没有合并任何边，图不连通
            return -1

    return total_weight
```
### 补图连通分量 + 缩点 Kruskal（用于解决0边很多的情况）
```python
import sys

def solve() -> None:
    input = sys.stdin.readline
    n, m = map(int, input().split())
    adj = [[] for _ in range(n + 1)]          # 正权边的邻接表
    edges = []                                # 存所有正权边 (u, v, w)

    for _ in range(m):
        u, v, w = map(int, input().split())
        adj[u].append(v)
        adj[v].append(u)
        edges.append((u, v, w))

    # ---------- 1. 找出所有0边连通分量（补图的连通块）----------
    unvisited = set(range(1, n + 1))          # 还未被分配分量的点
    comp_id = [-1] * (n + 1)                  # 每个点所属的分量编号
    comp_cnt = 0

    while unvisited:
        start = next(iter(unvisited))
        unvisited.remove(start)
        queue = [start]                       # 用列表模拟栈（DFS顺序，无所谓）
        while queue:
            u = queue.pop()
            comp_id[u] = comp_cnt             # 标记当前分量

            # 当前点 u 的所有正权邻居
            neigh = set(adj[u])               # 转为 set 加速判断
            # 临时移除这些邻居（它们不能通过0边直接从 u 到达）
            tmp = []
            for v in neigh:
                if v in unvisited:
                    unvisited.remove(v)
                    tmp.append(v)

            # 此时 unvisited 中剩下的点都是与 u 有0边的点
            zero_neighbors = list(unvisited)  # 复制出来
            for v in zero_neighbors:
                queue.append(v)               # 加入队列，继续BFS
            unvisited.clear()                 # 这些点已永久删除

            # 恢复之前临时移除的邻居（它们还在等待被其他点通过0边访问）
            for v in tmp:
                unvisited.add(v)

        comp_cnt += 1

    # ---------- 2. 对分量缩点，跑 Kruskal ----------
    parent = list(range(comp_cnt))
    rank = [0] * comp_cnt

    def find(x):
        while parent[x] != x:
            parent[x] = parent[parent[x]]
            x = parent[x]
        return x

    def union(x, y):
        rx, ry = find(x), find(y)
        if rx == ry:
            return False
        if rank[rx] < rank[ry]:
            parent[rx] = ry
        elif rank[rx] > rank[ry]:
            parent[ry] = rx
        else:
            parent[ry] = rx
            rank[rx] += 1
        return True

    # 收集连接不同分量的正权边
    useful = []
    for u, v, w in edges:
        cu, cv = comp_id[u], comp_id[v]
        if cu != cv:
            useful.append((w, cu, cv))

    useful.sort()                             # 按权值从小到大排序

    total = 0
    used = 0
    for w, cu, cv in useful:
        if union(cu, cv):
            total += w
            used += 1
            if used == comp_cnt - 1:
                break

    print(total)


if __name__ == "__main__":
    solve()
```
## 拓扑排序算法
### DFS：⽤于对有向⽆环图（DAG）进⾏拓扑排序。
```python
def dfs_topological_sort(vertices, edges):
    """
    用DFS进行拓扑排序（递归版）
    :return: 拓扑排序列表，有环则返回空列表
    """
    graph = defaultdict(list)
    for u, v in edges:
        graph[u].append(v)

    visited = [0] * vertices  # 0=未访问, 1=访问中, 2=已完成
    topo_order = []
    has_cycle = False

    def dfs(node):
        nonlocal has_cycle
        visited[node] = 1       # 标记为“访问中”
        for neighbor in graph[node]:
            if visited[neighbor] == 0:
                dfs(neighbor)
            elif visited[neighbor] == 1:
                has_cycle = True    # 发现回边，有环啦！(°ロ°)
        visited[node] = 2       # 标记为“已完成”
        topo_order.append(node) # 回溯时记录，注意是倒序

    for i in range(vertices):
        if visited[i] == 0:
            dfs(i)
            if has_cycle:
                return []       # 检测到环，直接跑路喵

    return topo_order[::-1]     # 反转才是正确的拓扑序
```
### Kahn算法 / BFS ：⽤于对有向⽆环图进⾏拓扑排序（入度）。
### 关键路径算法
```python
from collections import deque

def critical_path(n, edges, start, end):
    """
    n: 节点数 (编号0~n-1)
    edges: 列表 [(u, v, w), ...]
    start: 起点编号
    end: 终点编号
    return: 关键路径长度（最短工期），以及关键边列表
    """
    graph = [[] for _ in range(n)]
    indeg = [0]*n
    for u, v, w in edges:
        graph[u].append((v, w))
        indeg[v] += 1

    # 拓扑排序 + 最早时间
    topo = []
    q = deque([start])
    earliest = [-1]*n
    earliest[start] = 0
    while q:
        u = q.popleft()
        topo.append(u)
        for v, w in graph[u]:
            if earliest[v] < earliest[u] + w:
                earliest[v] = earliest[u] + w
            indeg[v] -= 1
            if indeg[v] == 0:
                q.append(v)

    total_time = earliest[end]

    # 反向图求最晚时间
    rgraph = [[] for _ in range(n)]
    for u, v, w in edges:
        rgraph[v].append((u, w))
    latest = [float('inf')]*n
    latest[end] = total_time
    for u in reversed(topo):
        for p, w in rgraph[u]:
            if latest[p] > latest[u] - w:
                latest[p] = latest[u] - w

    # 找关键边
    critical_edges = []
    for u, v, w in edges:
        if latest[v] - earliest[u] - w == 0:
            critical_edges.append((u, v, w))

    return total_time, critical_edges
```
## 强连通分量算法
通过⼀种叫作强连通单元（Strongly Connected Components，SCC）的图算法，可以找出图中⾼度连通的顶点簇。对于图G，强连通单元C为最⼤的顶点⼦集 ，其中对于每⼀对顶点 ，都有⼀条从v到w的路径和⼀条从w到v的路径。
### Kosaraju算法 / 2 DFS：⽤于找到有向图中的所有强连通分量。
```python
def kosaraju_scc(vertices, edges):
    """
    Kosaraju算法找强连通分量
    :param vertices: 节点数 (0 ~ vertices-1)
    :param edges: 有向边列表 [(u, v), ...]
    :return: 每个节点的SCC编号列表，编号从0开始
    """
    from collections import defaultdict

    # 1. 建正向图和反向图
    graph = defaultdict(list)
    rev_graph = defaultdict(list)
    for u, v in edges:
        graph[u].append(v)
        rev_graph[v].append(u)   # 注意反向喵～

    # 2. 第一遍DFS：收集完成时间顺序
    visited = [False] * vertices
    finish_order = []            # 按完成时间从早到晚存，最后要反转

    def dfs_forward(u):
        visited[u] = True
        for v in graph[u]:
            if not visited[v]:
                dfs_forward(v)
        finish_order.append(u)   # 回溯时加入

    for i in range(vertices):
        if not visited[i]:
            dfs_forward(i)

    # 3. 第二遍DFS：在反向图上按 finish_order 逆序找连通块
    scc_id = [-1] * vertices
    current_scc = 0

    def dfs_reverse(u):
        scc_id[u] = current_scc
        for v in rev_graph[u]:
            if scc_id[v] == -1:
                dfs_reverse(v)

    # 倒序遍历 finish_order
    for node in reversed(finish_order):
        if scc_id[node] == -1:   # 还没被分配SCC
            dfs_reverse(node)
            current_scc += 1

    return scc_id, current_scc
```
### Tarjan算法：⽤于找到有向图中的所有强连通分量。
```python
def tarjan_scc(vertices, edges):
    """
    Tarjan 算法找强连通分量
    :param vertices: 节点数（0 ~ vertices-1）
    :param edges: 有向边列表 [(u, v), ...] 表示 u → v
    :return: 每个节点所属的 SCC 编号列表，编号从0开始
    """
    from collections import defaultdict

    graph = defaultdict(list)
    for u, v in edges:
        graph[u].append(v)

    dfn = [-1] * vertices          # 时间戳（深度优先数）
    low = [-1] * vertices          # 能够追溯到的最早祖先
    in_stack = [False] * vertices  # 是否在栈中
    stack = []
    scc_id = [-1] * vertices       # 每个节点属于哪个SCC
    timestamp = [0]                # 时间戳计数器（用列表以便在闭包里修改）
    scc_count = [0]                # SCC 计数

    def dfs(u):
        dfn[u] = low[u] = timestamp[0]
        timestamp[0] += 1
        stack.append(u)
        in_stack[u] = True

        for v in graph[u]:
            if dfn[v] == -1:       # 没访问过的邻居
                dfs(v)
                low[u] = min(low[u], low[v])
            elif in_stack[v]:      # 已经在栈里，说明 v 是 u 的祖先（回边）
                low[u] = min(low[u], dfn[v])

        # 如果 u 是当前 SCC 的根（不能回到更早的祖先）
        if low[u] == dfn[u]:
            while True:
                top = stack.pop()
                in_stack[top] = False
                scc_id[top] = scc_count[0]
                if top == u:
                    break
            scc_count[0] += 1

    for i in range(vertices):
        if dfn[i] == -1:
            dfs(i)

    return scc_id, scc_count[0]
```
## 欧拉路径
```python
# 欧拉路径
from collections import defaultdict

def find_euler_path(n, edges):
    """
    返回无向图的欧拉路径（节点序列）。
    若无欧拉路径则返回空列表。
    n: 节点数（0..n-1）
    edges: [(u, v), ...] 无向边
    """
    graph = defaultdict(list)
    degree = [0] * n
    for u, v in edges:
        graph[u].append(v)
        graph[v].append(u)
        degree[u] += 1
        degree[v] += 1

    # 检查存在性：奇度顶点只能是 0 或 2 个
    odd_nodes = [i for i in range(n) if degree[i] % 2 == 1]
    if len(odd_nodes) not in [0, 2]:
        return []

    # 确定起点（奇度点优先，否则任意非0度点）
    start = odd_nodes[0] if odd_nodes else next((i for i in range(n) if degree[i] > 0), 0)

    # 使用栈模拟递归，避免Python递归深度
    path = []
    stack = [start]
    # 邻接表遍历时，用索引或pop来删边；这里用倒序pop边
    while stack:
        u = stack[-1]
        if graph[u]:
            v = graph[u].pop()
            # 从 v 的邻接表中删除 u（因为是无向边）
            graph[v].remove(u)
            stack.append(v)
        else:
            path.append(stack.pop())

    # 检查是否使用了所有边（图可能不连通）
    if len(path) != len(edges) + 1:
        return []
    return path[::-1]   # 逆序即为欧拉路径
```
# 树
## 二叉搜索树
```python
# 二叉搜索树的中序遍历是递增的
class Solution:
    def kthSmallest(self, root: Optional[TreeNode], k: int) -> int:
        ans = 0
        def dfs(node: Optional[TreeNode]) -> None:
            nonlocal k, ans
            if node is None or k <= 0:
                return
            dfs(node.left)  # 左
            k -= 1
            if k == 0:
                ans = node.val  # 根
            dfs(node.right)  # 右
        dfs(root)
        return ans
```
建立一个二叉搜索树
```python
# 定义⼆叉树结点
class TreeNode:
    def __init__(self, val):
        self.val = val
        self.left = None
        self.right = None
# 插⼊结点到 BST 中
def insert(root, val):
    if not root:
        return TreeNode(val)
    if val < root.val:
        root.left = insert(root.left, val)
    elif val > root.val:
        root.right = insert(root.right, val)
    return root
```
## Huffman
```python
import heapq
from collections import Counter

# ---------- 节点类 ----------
class Node:
    def __init__(self, char, freq):
        self.char = char      # 字符（叶子节点才有）
        self.freq = freq      # 频率
        self.left = None      # 左孩子
        self.right = None     # 右孩子

    # 为了让堆能比较节点，定义 < 运算符
    def __lt__(self, other):
        return self.freq < other.freq

# ---------- 构建 Huffman 树 ----------
def build_huffman_tree(text):
    """输入字符串，返回 Huffman 树的根节点"""
    if not text:
        return None

    # 1. 统计频率
    freq = Counter(text)

    # 2. 为每个字符创建叶子节点，放入最小堆
    heap = [Node(ch, f) for ch, f in freq.items()]
    heapq.heapify(heap)

    # 3. 不断合并最小的两个节点
    while len(heap) > 1:
        left = heapq.heappop(heap)   # 最小频率
        right = heapq.heappop(heap)  # 次小频率
        # 合并成新节点，频率相加，字符为空
        merged = Node(None, left.freq + right.freq)
        merged.left = left
        merged.right = right
        heapq.heappush(heap, merged)

    return heap[0]  # 最后剩下的就是根节点

# ---------- 生成编码表 ----------
def build_codes(root):
    """从 Huffman 树生成编码字典 {字符: 二进制字符串}"""
    codes = {}

    def dfs(node, code):
        if node is None:
            return
        # 叶子节点：存储编码
        if node.char is not None:
            codes[node.char] = code
            return
        # 非叶子：左0 右1
        dfs(node.left, code + '0')
        dfs(node.right, code + '1')

    dfs(root, '')
    return codes

# ---------- 编码 ----------
def huffman_encode(text):
    """输入字符串，返回 (编码字符串, Huffman树根)"""
    root = build_huffman_tree(text)
    codes = build_codes(root)
    encoded = ''.join(codes[ch] for ch in text)
    return encoded, root

# ---------- 解码 ----------
def huffman_decode(encoded, root):
    """输入编码字符串和Huffman树，返回原始字符串"""
    decoded = []
    node = root
    for bit in encoded:
        if bit == '0':
            node = node.left
        else:  # '1'
            node = node.right
        # 到达叶子节点
        if node.char is not None:
            decoded.append(node.char)
            node = root  # 回到根，继续下一个字符
    return ''.join(decoded)
```
## LCA最低公共祖先
```python
# 倍增法
def build_lca(n: int, edges: list, root: int = 0):
    """
    输入：节点数n，无向边列表edges，根节点root
    返回：一个查询函数 lca(u, v) 返回u,v的最近公共祖先
    """
    # 建图
    graph = [[] for _ in range(n)]
    for u, v in edges:
        graph[u].append(v)
        graph[v].append(u)

    LOG = (n).bit_length()          # 最大跳跃指数
    depth = [0] * n
    up = [[-1] * LOG for _ in range(n)]   # up[u][i] 存 u 的 2^i 级祖先

    # ---------- 1. DFS 初始化深度与父节点 ----------
    def dfs(u: int, parent: int):
        up[u][0] = parent
        for v in graph[u]:
            if v != parent:
                depth[v] = depth[u] + 1
                dfs(v, u)

    dfs(root, -1)

    # ---------- 2. 预处理所有 2^i 祖先 ----------
    for i in range(1, LOG):
        for u in range(n):
            if up[u][i-1] != -1:
                up[u][i] = up[ up[u][i-1] ][i-1]

    # ---------- 3. 返回查询函数（闭包）----------
    def lca(u: int, v: int) -> int:
        # 让 u 成为较深的那个
        if depth[u] < depth[v]:
            u, v = v, u

        # 提升 u 到与 v 相同深度
        diff = depth[u] - depth[v]
        i = 0
        while diff:
            if diff & 1:
                u = up[u][i]
            diff >>= 1
            i += 1

        if u == v:
            return u

        # 一起向上跳，从大到小
        for i in range(LOG - 1, -1, -1):
            if up[u][i] != up[v][i]:
                u = up[u][i]
                v = up[v][i]

        return up[u][0]

    return lca
```
## 前缀树
```python
class TrieNode:
    def __init__(self):
        self.children = {}
        self.is_end = False

class Trie:
    def __init__(self):
        self.root = TrieNode()

    def insert(self, word: str) -> None:
        node = self.root
        for ch in word:
            if ch not in node.children:
                node.children[ch] = TrieNode()
            node = node.children[ch]
        node.is_end = True

    def search(self, word: str) -> bool:
        node = self._find_node(word)
        return node is not None and node.is_end

    def startsWith(self, prefix: str) -> bool:
        node = self._find_node(prefix)
        return node is not None

    def _find_node(self, prefix: str):
        """辅助方法：返回prefix最后一个字符所在的节点，不存在则返回None"""
        node = self.root
        for ch in prefix:
            if ch not in node.children:
                return None
            node = node.children[ch]
        return node
```
## 树形DP
即利用子树的结果，更新根节点的结果，由于树的结构，通常用dfs实现
```python
# 选课问题，树上背包
def tree_knapsack(n, m, scores, edges):
    """
    n: 课程数（0 为虚拟根，课程编号 1..n）
    m: 可选课程总数
    scores: 学分列表，索引 0..n
    edges: 树边列表，每条边 (parent, child)
    """
    graph = [[] for _ in range(n + 1)]
    for p, c in edges:
        graph[p].append(c)

    # dp[u][j]：子树 u 选 j 门课的最大值，注意包含 u 本身
    dp = [[0] * (m + 1) for _ in range(n + 1)]
    size = [0] * (n + 1)  # 子树大小，用来优化循环上界

    def dfs(u):
        size[u] = 1
        dp[u][1] = scores[u]   # 初始化：只选自己
        for v in graph[u]:
            dfs(v)
            # 类似分组背包，必须逆序枚举 j，正序枚举 k
            for j in range(min(m, size[u] + size[v]), 0, -1):
                # k 是分配给子节点 v 的课数，至少 1，最多 size[v] 且 < j
                for k in range(1, min(size[v], j - 1) + 1):
                    dp[u][j] = max(dp[u][j], dp[u][j - k] + dp[v][k])
            size[u] += size[v]

    dfs(0)
    return dp[0][m]  # 虚拟根选 m 门课的最大值
```
```python
"""
树的重心 (Centroid of a Tree)
================================
定义：
    在一棵有 n 个节点的树中，删除节点 v 后，树会分裂成若干连通块。
    记 f(v) = 删除 v 后最大连通块的节点数。
    树的重心就是使 f(v) 最小的节点。

性质：
    1. 以重心为根时，它的每个子树的节点数都 ≤ n/2。
       （反之，满足该条件的节点一定是重心）
    2. 一棵树最多有 2 个重心，且若有 2 个，它们必然相邻（n 为偶数）。
    3. 重心到树上所有其他节点的距离之和最小（无权树的中位点）。
    4. 求重心只需一次 DFS，时间复杂度 O(n)。
    5. 重心常用于点分治，保证每次递归子树大小减半，递归深度 O(log n)。
"""

def find_centroid(n, edges):
    """
    输入：
        n : int, 树的节点数（编号 0..n-1）
        edges : List[Tuple[int, int]], 无向边列表
    输出：
        centroids : List[int], 树的所有重心（1个或2个）
    """
    # 1. 建图
    graph = [[] for _ in range(n)]
    for u, v in edges:
        graph[u].append(v)
        graph[v].append(u)

    size = [0] * n               # size[u] = 子树 u 的大小
    min_max_part = float('inf')  # 当前最小的 f(u)
    centroids = []               # 存放重心

    def dfs(u, parent):
        nonlocal min_max_part, centroids
        size[u] = 1
        max_part = 0  # u 的最大连通块大小（子树或上方块）

        for v in graph[u]:
            if v == parent:
                continue
            dfs(v, u)
            size[u] += size[v]
            # 记录最大子树大小
            max_part = max(max_part, size[v])

        # 计算“上方”连通块大小（即整棵树去掉子树 u 的部分）
        max_part = max(max_part, n - size[u])

        # 根据 f(u) = max_part 判断是否为重心
        if max_part < min_max_part:
            min_max_part = max_part
            centroids = [u]
        elif max_part == min_max_part:
            centroids.append(u)

    # 从节点 0 开始 DFS（任意起点均可）
    dfs(0, -1)
    return centroids
```
## 线段树
```python
class SegTree:
    def __init__(self, n, merge=max, init_val=0):
        """
        :param n: 原数组长度
        :param merge: 合并函数，比如 max, min, lambda a,b: a+b
        :param init_val: 查询时的初始值（取决于 merge）
                          max -> -inf，min -> inf，sum -> 0 等
        """
        self.n = n
        self.merge = merge
        self.init_val = init_val
        self.tree = [init_val] * (2 * n)

    def build(self, arr):
        """根据原始数组 arr 构建线段树"""
        n = self.n
        # 放入叶子
        for i in range(n):
            self.tree[n + i] = arr[i]
        # 从下往上合并
        for i in range(n - 1, 0, -1):
            self.tree[i] = self.merge(self.tree[i << 1], self.tree[i << 1 | 1])

    def update(self, idx, val):
        """单点更新：将下标 idx 的值改为 val"""
        i = idx + self.n
        self.tree[i] = val
        while i > 1:
            i >>= 1
            self.tree[i] = self.merge(self.tree[i << 1], self.tree[i << 1 | 1])

    def query(self, left, right):
        """
        区间查询，左闭右开 [left, right)
        返回 merge(self.tree[left..right-1])
        """
        l = left + self.n
        r = right + self.n
        res_left = self.init_val   # 从左边收集的结果
        res_right = self.init_val  # 从右边收集的结果
        while l < r:
            if l & 1:
                res_left = self.merge(res_left, self.tree[l])
                l += 1
            if r & 1:
                r -= 1
                res_right = self.merge(self.tree[r], res_right)
            l >>= 1
            r >>= 1
        return self.merge(res_left, res_right)
```