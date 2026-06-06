### sys库

```python
import sys

sys.setrecursionlimit(10**6)

it = iter(map(int, sys.stdin.read().split()))
#EOF输入：mac(^D), win(^Z)
```

## 图论

### dijkstra

```python
import heapq
n, m = map(int, input().split())
G = [[] for _ in range(n + 1)]
# G[u].append((v, w))

inf = 10**15
def dijkstra(G, d, S):
    vis = [0] * (n + 1)
    q = []
    d[S] = 0
    heapq.heappush(q, (0, S))
    while q:
        _, u = heapq.heappop(q)
        if vis[u]: continue
        vis[u] = 1
        for v, w in G[u]:
            if d[v] > d[u] + w:
                d[v] = d[u] + w
                heapq.heappush(q, (d[v], v))

d1 = [inf] * (n + 1)
d2 = [inf] * (n + 1)
dijkstra(G, d1, 1)
dijkstra(H, d2, 1)
```

### 0-W kruskal

```python
n, m = map(int, input().split())
G = [[] for _ in range(n+1)]
E = []
for i in range(m):
    u, v, w = map(int, input().split())
    G[u].append(v)
    G[v].append(u)
    E.append((w, u, v))
q = [i for i in range(n+1)]
qh = 1; qt = 0
vis = [0]*(n+1)
dcnt = 0
d = [0]*(n+1)
def bfs():
    global qh, qt, dcnt
    dcnt += 1
    qt += 1
    while qh <= qt:
        u = q[qh]
        qh += 1
        d[u] = dcnt
        q1, q2 = [], []
        for v in G[u]:
            vis[v] = 1
        for v in q[qt+1:]:
            if vis[v]: q1.append(v)
            else: q2.append(v)
        for v in G[u]:
            vis[v] = 0
        q[qt+1:] = q2 + q1
        qt += len(q2)
while qt < n:
    bfs()
fa = [i for i in range(dcnt+1)]
def getfa(u):
    if u != fa[u]: fa[u] = getfa(fa[u])
    return fa[u]
E.sort()
ans = 0
for w, u, v in E:
    fu, fv = getfa(d[u]), getfa(d[v])
    if fu != fv:
        ans += w
        fa[fu] = fv
print(ans)
```

### 欧拉路

```python
import sys
sys.setrecursionlimit(10**6)
T = int(input())
for _ in range(T):
    n = int(input())
    a = ['']*n
    def toint(x):
        return ord(x) - ord('a')
    G = [[] for _ in range(40)]
    rd = [0]*40
    cd = [0]*40
    for i in range(n):
        a[i] = input().rstrip()
        u, v = toint(a[i][0]), toint(a[i][-1])
        G[u].append((a[i], i, v))
        rd[v] += 1
        cd[u] += 1
    S = -1
    tot = 0
    for u in range(40):
        G[u].sort()
        if cd[u] > rd[u]:
            S = u
            tot += cd[u] - rd[u]
    if tot > 1:
        print('***')
        continue
    if S == -1:
        for u in range(40):
            if cd[u]:
                S = u
                break
    ans = []
    vis = [0]*n
    def dfs(u, ie):
        for _, e, v in G[u]:
            if vis[e]: continue
            vis[e] = 1
            dfs(v, e)
        if ie != -1:
            ans.append(a[ie])
    dfs(S, -1)
    ans.reverse()
    if len(ans) == n:
        print('.'.join(ans))
    else:
        print('***')
```

### tarjan

#### 1. popular cows (scc)

有向图中，求被所有点可达之点数目。scc缩点

```python
import sys
sys.setrecursionlimit(100000)
n, m = map(int, input().split())
G = [[] for _ in range(n+1)]
for _ in range(m):
    u, v = map(int, input().split())
    G[u].append(v)
dfn = [0]*(n+1)
low = [0]*(n+1)
cnt = 0
ind = [0]*(n+1)
st = []
top = [0]*(n+1)
def tarjan(u):
    global cnt
    cnt += 1
    dfn[u], low[u] = cnt, cnt
    ind[u] = 1
    st.append(u)
    for v in G[u]:
        if not dfn[v]:
            tarjan(v)
            low[u] = min(low[u], low[v])
        elif ind[v]:
            low[u] = min(low[u], dfn[v])
    if low[u] == dfn[u]:
        while st:
            v = st.pop()
            top[v] = u
            ind[v] = 0
            if v == u: break

for u in range(1, n+1):
    if not dfn[u]:
        tarjan(u)

H = [[] for _ in range(n+1)]
deg = [0]*(n+1)
for u in range(1, n+1):
    for v in G[u]:
        if top[u] != top[v]:
            H[top[u]].append(top[v])
            deg[top[u]] += 1

rt, cntu = 0, 0
for u in range(1, n+1):
    # print(u, top[u], low[u], dfn[u])
    if top[u] == u:
        if deg[u] == 0:
            cntu += 1
            rt = u
if cntu > 1:
    print(0)
else:
    ans = 0
    for u in range(1, n + 1):
        if top[u] == rt:
            ans += 1
    print(ans)
```

#### 2.割边

```python
import sys
sys.setrecursionlimit(100000)
n, m = map(int, input().split())
G = [[] for _ in range(n + 1)]
edges = []          # 存储每条无向边的两个端点 (u, v)
edge_id = 0
for _ in range(m):
    u, v = map(int, input().split())
    edges.append((u, v))
    G[u].append((v, edge_id))
    G[v].append((u, edge_id))
    edge_id += 1

dfn = [0] * (n + 1)
low = [0] * (n + 1)
cnt = 0
bridges = []        # 存放桥的边编号 (eid)
def tarjan(u, parent_edge):
    global cnt
    cnt += 1
    dfn[u] = low[u] = cnt
    for v, eid in G[u]:
        if eid == parent_edge:      # 跳过回到父节点的同一条无向边
            continue
        if not dfn[v]:
            tarjan(v, eid)
            low[u] = min(low[u], low[v])
            if low[v] > dfn[u]:     # 割边判定条件
                bridges.append(eid)
        else:
            low[u] = min(low[u], dfn[v])

for i in range(1, n + 1):
    if not dfn[i]:
        tarjan(i, -1)

if bridges:
    for eid in sorted(bridges):
        u, v = edges[eid]
        print(u, v)
else:
    print("No bridges")
```

#### 3. bcc

点双连通分量（BCC，顶点双连通）

**定义**：一个极大子图，删除其中任意一个顶点后，子图仍然连通（即不存在割点）。

**算法要点**：

- 使用栈 **存储边**（而不是节点）。
- 当 `low[v] >= dfn[u]` 时（`u` 不是根，或根有多个子树），说明 `u` 是一个割点（或根），此时从栈中弹出边，直到弹出 `(u, v)` 为止。这些边构成一个点双连通分量。
- 对于根节点，如果它的孩子数 ≥ 2，则根是割点，同样会触发 BCC 划分。

### prim

```python
import sys
import heapq

def main():
    n, m = map(int, input().split())
    G = [[] for _ in range(n + 1)]
    for _ in range(m):
        u, v, w = map(int, input().split())
        G[u].append((v, w))
        G[v].append((u, w))
    vis = [False] * (n + 1)
    q = [(0, 1)]          # (权值, 节点)，从节点1开始
    total = 0
    cnt = 0
    while q and cnt < n:
        w, u = heapq.heappop(q)
        if vis[u]:
            continue
        vis[u] = True
        total += w
        cnt += 1
        for v, weight in G[u]:
            if not vis[v]:
                heapq.heappush(q, (weight, v))
    # 若图连通，cnt == n
    if cnt == n:
        print(total)
    else:
        print("图不连通")
if __name__ == "__main__":
    main()
```

### 关键路径

```python
from collections import deque

n, m = map(int, input().split())
G = [[] for _ in range(n + 1)]
H = [[] for _ in range(n + 1)]
rd = [0]*(n + 1)
cd = [0]*(n + 1)
for _ in range(m):
    u, v, w = map(int, input().split())
    G[u].append((v, w))
    H[v].append((u, w))
    rd[v] += 1
    cd[u] += 1
for u in range(1, n + 1):
    G[u].sort()
d1 = [0]*(n + 1)
d2 = [0]*(n + 1)
def bfs(G, rd, d):
    q = deque()
    for u in range(1, n + 1):
        if rd[u] == 0:
            d[u] = 0
            q.append(u)
    while q:
        u = q.popleft()
        for v, w in G[u]:
            d[v] = max(d[v], d[u]+w)
            rd[v] -= 1
            if rd[v] == 0:
                q.append(v)
bfs(G, rd, d1)
mx = max(d1)
print(mx)
bfs(H, cd, d2)
vis = [0]*(n + 1)
for u in range(1, n + 1):
    if d1[u] + d2[u] == mx:
        for v, w in G[u]:
            if d1[v] + d2[v] == mx and d1[v] - d1[u] == w:
                print(u, v)
```

## 树

### 线段树

无懒标记，单点修改，求区间max：（注意调用时是1, 0, n-1还是1, 1, n）

```python
t = [0]*(N*4)
def insert(o, l, r, idx, x):
    if (l == r):
        t[o] = x
        return
    mid = (l+r)//2
    if idx <= mid:
        insert(o<<1, l, mid, idx, x)
    else:
        insert(o<<1|1, mid+1, r, idx, x)
    t[o] = max(t[o<<1], t[o<<1|1])

def query(o, l, r, b, e):
    if b > e: return 0
    if (b <= l and r <= e): return t[o]
    mid = (l+r)//2
    mx = 0
    if b <= mid: mx = max(mx, query(o<<1, l, mid, b, e))
    if e > mid: mx = max(mx, query(o<<1|1, mid+1, r, b, e))
    return mx
```

### 树状数组

#### 1. 逆序对数

多组读入，离散化后求逆序对数

树状数组维护1～n，addx不能在0处，由sumx给出1～x的和

```python
import bisect
def addx(x, val):
    while x < n+1:
        t[x] += val
        x += x&(-x)
def sumx(x):
    ret = 0
    while x > 0:
        ret += t[x]
        x -= x&(-x)
    return ret
while 1:
    n = int(input())
    if n == 0: break
    a = [0]*n
    for i in range(n):
        a[i] = int(input())
    b = a.copy()
    b.sort()
    for i in range(n):
        a[i] = bisect.bisect_left(b, a[i]) + 1
    # print(*a)
    t = [0]*(n+1)
    s = 0
    for i, x in enumerate(a):
        s += i - sumx(x)
        addx(x, 1)
    print(s)
```

### 哈夫曼编码树

```
样例：
3
g 4
d 8
c 10
dc
110
===
110
dc
```

```python
import heapq
n = int(input())
a = []
ch = [(-1, -1) for _ in range(n*2+2)]
c = ['' for _ in range(n*2+2)]
ln = {}
cnt = 0
for i in range(n):
    x, y = input().split()
    a.append((int(y), x, cnt))
    c[cnt] = x
    cnt += 1
heapq.heapify(a)
while len(a) > 1:
    w1, x1, u1 = heapq.heappop(a)
    w2, x2, u2 = heapq.heappop(a)
    ch[cnt] = (u1, u2)
    heapq.heappush(a, (w1+w2, min(x1, x2), cnt))
    cnt += 1
root = a[0][2]
def dfs1(u, s):
    if u < n:
        ln[c[u]] = s
        return
    dfs1(ch[u][0], s+'0')
    dfs1(ch[u][1], s+'1')
dfs1(root, '')

try:
    while 1:
        s = input().rstrip()
        if s[0] not in '01':
            print(''.join(ln[x] for x in s if x in ln))
            continue
        l = s
        u = root
        ans = []
        for x in l:
            u = ch[u][ord(x)-ord('0')]
            if u < n:
                ans.append(c[u])
                u = root
        print(''.join(ans))
except:
    pass
```

### Trie树

```python
class Trie:
    class Node:
        def __init__(self):
            self.ch = {}
            self.cnt = 0
    def __init__(self):
        self.root = Trie.Node()
    def insert(self, word: str) -> None:
        u = self.root
        for c in word:
            if c not in u.ch: u.ch[c] = Trie.Node()
            u = u.ch[c]
        u.cnt += 1
    def search(self, word: str) -> bool:
        u = self.root
        for c in word:
            if c in u.ch: u = u.ch[c]
            else: return False
        return bool(u.cnt)
```

## 算法

### 双指针、栈、队列

#### 1. 最长不重复子序列

给定s，求没有重复字符的子序列的最大长度。双指针法

用字典记录，初始`i=j=0`，`i, j`均为端点，长度`i-j+1`

```python
from collections import defaultdict

s = input()
n = len(s)
j = 0
dic = defaultdict(int)
mx = 0
for i in range(n):
    dic[s[i]] += 1
    while dic[s[i]] > 1:
        dic[s[j]] -= 1
        j += 1
    mx = max(mx, i-j+1)
print(mx)
```

#### 2. 狭路相逢

正数向右走，负数向左走，碰到互相减，生命值碰到或跨过0就死，求最后状态

```python
n = int(input())
a = list(map(int, input().split()))
b = []
for x in a:
    if x > 0:
        b.append(x)
    else:
        while len(b) and b[-1] > 0:
            x += b[-1]
            b.pop()
            if x >= 0: break
        # x>0 x<0
        if x != 0:
            b.append(x)
print(len(b))
print(*b)
```

#### 3. 中序表达式

输入中序表达式，输出后序表达式

```python
T = int(input())
pri = {'(':0, ')':-1, '$':-1, '+':1, '-':1, '*':2, '/':2}
for _ in range(T):
    s = '(' + input().rstrip() + ')'
    j = 0
    n = len(s)
    nums = []
    ops = []
    while j < n:
        if '0' <= s[j] <= '9' or s[j] == '.':
            j0 = j
            while j < n and ('0' <= s[j] <= '9' or s[j] == '.'): j += 1
            num = s[j0:j]
            nums.append(num)
        else:
            op0 = s[j]
            if op0 != '(':
                while ops and pri[s[j]] <= pri[ops[-1]]:
                    if ops[-1] == '(' and op0 == ')':
                        ops.pop(); break
                    # print(ops, nums)
                    op = ops.pop(); num2 = nums.pop(); num1 = nums.pop()
                    nums.append(num1+' '+num2+' '+op)
            if op0 != ')': ops.append(op0)
            j += 1
    print(nums[0])
```

 #### 4. 判断合法出栈序列

```python
x = input().rstrip()
n = len(x)
def check(s):
    if len(s) != n: return 0
    st = []; i = 0
    for ch in s:
        if st and st[-1] == ch: st.pop()
        else:
            while i < n and x[i] != ch: st.append(x[i]); i += 1
            if i == n: return 0
            else: i += 1
    if st or i != n: return 0
    return 1
try:
    while 1:
        s = input().rstrip()
        print(["NO", "YES"][check(s)])
except EOFError:
    pass
```

#### 5. N个点求最小宽度满足区间内高度差不小于D

```python
#include <bits/stdc++.h>
using namespace std;
int n, D;
const int N = 1e5+20;
pair<int, int> r[N];
pair<int, int> ql[N], qr[N];
int qlh, qlt, qrh, qrt;
int main() {
    unsigned W = -1;
    cin >> n >> D;
    for (int i=0; i<n; i++)
        cin >> r[i].first >> r[i].second;
    sort(r, r+n);
    qlh = qlt = qrh = qrt = 0;
    for (int i=0, j=0; i<n; i++) {
        if (qlh != qlt and ql[qlh].first < i) qlh++;
        if (qrh != qrt and qr[qrh].first < i) qrh++;
        while (j<n and ql[qlh].second-qr[qrh].second < D) {
            int y = r[j].second; // j, y
            while (qlh != qlt and ql[qlt-1].second <= y) {
                qlt--;
            }
            ql[qlt] = make_pair(j, y); qlt++;
            while (qrh != qrt and qr[qrt-1].second >= y) {
                qrt--;
            }
            qr[qrt] = make_pair(j, y); qrt++;
            j++;
        }
        if (ql[qlh].second-qr[qrh].second >= D) {
            //cout << i << ' ' << j << endl;
            W = min(W, unsigned(r[j-1].first-r[i].first));
        }
    }
    cout << int(W) << endl;
    return 0;
}
```

### dp，动态规划

#### 1. 旅行售货商

n个点，无向图，输入邻接矩阵`cost[i, j]`（对称），售货商要从一个城市出发，遍历所有点后回到出发点，求最小花费

转移方程：`l[j][s|(1<<j)] = min(l[j][s|(1<<j)], l[i][s] + w[i][j])`

遍历顺序：`s, i, j`

结果：`ans = min(ans, l[i][(1<<n)-1] + w[i][0])`

```python
n = int(input())
w = [list(map(int, input().split())) for _ in range(n)]
inf = int(1e9)
l = [[inf]*(1<<n) for _ in range(n)]
l[0][1] = 0
for s in range(1, 1<<n):
    for i in range(n):
        if s>>i & 1:
            for j in range(n):
                if not(s>>j & 1):
                    l[j][s | (1<<j)] = min(l[j][s | (1<<j)], l[i][s] + w[i][j])
ans = inf
for i in range(n):
    ans = min(ans, l[i][(1<<n)-1] + w[i][0])
print(ans)
```

### 贪心、杂题

#### 1. 排布网格最少交换次数

输入`n*n`的01阵，每次可以交换相邻两行，求让矩阵下三角的最小交换次数

相当于：输入`n`个数，每次可交换相邻两数，求使`a[i]<=i`的最小次数

贪心，从1往n考虑

```python
def minSwaps(self, grid: List[List[int]]) -> int:
    n = len(grid)
    p = [-1]*n
    ans = 0
    for i in range(n):
        for j in range(n-1, -1, -1):
            if grid[i][j]:
                p[i] = j
                break
    for i in range(n):
        for j in range(i, n):
            if p[j] <= i:
                p[i:j+1] = p[j:j+1] + p[i:j]
                ans += j-i
                break
        else:
            return -1
    return ans
```

#### 2. 数字华容道

输入`n*n`个元素，输出是否可以操作为`1 ~ (n*n-1), 0`的顺序。将`0`换成`n*n`，则逆序对数目奇偶性xor所有`((i+j)&1)`是一个不变量

```python
n = int(input())
a = [list(map(int, input().split())) for _ in range(n)]
b = [0]*(n*n+1) # 1~n*n
ans = 0
def getx(x): # 0~n*n
    ret = 0
    while x>0:
        ret += b[x]
        x -= x&(-x)
    return ret
def addx(x, v): # 1~n*n
    while x <= n*n:
        b[x] += v
        x += x&(-x)
tmp = 0
for i in range(n):
    for j in range(n):
            if not a[i][j]:
                ans ^= (i&1) ^ (j&1)
                a[i][j] = n*n
            ans ^= (tmp - getx(a[i][j]))&1
            # print(list(getx(k) for k in range(n*n+1)))
            addx(a[i][j], 1)
            tmp += 1
print("yes" if ans == 0 else "no")
```

#### 3. LRU缓存

`put(key, value)` 如果关键字 `key` 已经存在，则变更其数据值 `value` ；如果不存在，则向缓存中插入该组 `key-value` 。如果插入操作导致关键字数量超过 `capacity` ，则应该 **逐出** 最久未使用的关键字。

哈希表mp需要指向链表节点。三个操作：把mp[key]节点断联后放到最前；新建节点放到最前；删除尾部

```python
class Node:
    def __init__(self):
        self.key = 0
        self.val = 0
        self.l, self.r = None, None
    def out(self):
        self.l.r, self.r.l = self.r, self.l
    def into(self, hd):
        self.l, self.r = hd, hd.r 
        hd.r.l = self
        hd.r = self
class LRUCache:
    def __init__(self, capacity: int):
        self.capacity = capacity
        self.cnt = 0
        self.mp = {}
        self.hd, self.tl = Node(), Node()
        self.hd.r = self.tl
        self.tl.l = self.hd
    def get(self, key: int) -> int:
        if key not in self.mp: return -1
        x = self.mp[key]
        x.out()
        x.into(self.hd)
        return x.val
    def put(self, key: int, value: int) -> None:
        if key not in self.mp:
            x = Node(); x.key = key; x.val = value; self.cnt += 1
            self.mp[key] = x
            if self.cnt > self.capacity:
                y = self.tl.l; y.out(); self.mp.pop(y.key); self.cnt -= 1
        else:
            x = self.mp[key]; x.val = value; x.out()
        x.into(self.hd)

```

#### 4.启发式搜索 解数独

每次先找最小可能去搜，1000ms->20ms

```python
class Solution:
    fl = (1<<10) - 2
    def add(self, i, j):
        self.rem.add((i, j))
    def pop(self, i, j):
        self.rem.remove((i, j))
    def nxt(self):
        i0, j0, mx = -1, -1, 0
        for i, j in self.rem:
            y = (self.ln[i] | self.cn[j] | self.dn[i//3][j//3]).bit_count()
            if y > mx:
                i0, j0, mx = i, j, y
        return i0, j0
        
    def dfs(self):
        i, j = self.nxt()
        if i == -1:
            self.solved = 1
            return
        #(i+1, 0) if j == 8 else (i, j+1)
        if self.lock[i][j]:
            self.dfs()
            return
        self.pop(i, j)
        state = self.fl - (self.ln[i] | self.cn[j] | self.dn[i//3][j//3])
        for x in range(1, 10):
            if (state>>x)&1:
                self.ln[i] |= 1<<x; self.cn[j] |= 1<<x; self.dn[i//3][j//3] |= 1<<x
                self.board[i][j] = str(x)
                self.dfs()
                if self.solved: return
                self.ln[i] -= 1<<x; self.cn[j] -= 1<<x; self.dn[i//3][j//3] -= 1<<x
        self.add(i, j)
    def solveSudoku(self, board: List[List[str]]) -> None:
        """
        Do not return anything, modify board in-place instead.
        """
        self.lock = [[0]*9 for _ in range(9)]
        self.ln = [0]*9
        self.cn = [0]*9
        self.dn = [[0]*3 for _ in range(3)]
        for i in range(9):
            for j in range(9):
                if board[i][j] != '.':
                    self.lock[i][j] = 1
                    x = int(board[i][j])
                    self.ln[i] |= 1<<x
                    self.cn[j] |= 1<<x
                    self.dn[i//3][j//3] |= 1<<x
        self.rem = set()
        for i in range(9):
            for j in range(9):
                if not self.lock[i][j]:
                    self.add(i, j)
        self.board = board
        self.solved = 0
        self.dfs()
```

#### 5.骑士周游

```python
n = int(input())
sr, sc = map(int, input().split())
step = [(-1, 2), (1, -2), (-2, -1), (-2, 1), (-1, -2), (1, 2), (2, -1), (2, 1)]
G = [[] for _ in range(n*n)]
for i in range(n):
    for j in range(n):
        u = i*n+j
        for di, dj in step:
            if i+di in range(n) and j+dj in range(n):
                v = (i+di)*n+(j+dj)
                G[u].append(v)

state = [0]*(n*n)
def magic(u):
    q = []
    for v in G[u]:
        if not state[v]:
            tmp = 0
            for w in G[v]:
                if not state[w]:
                    tmp += 1
            q.append((tmp, v))
    return sorted(q, key=lambda x: x[0])
def dfs(u, k):
    if k == n*n:
        return 1
    for _, v in magic(u):
        state[v] = 1
        if dfs(v, k+1):
            return 1
        state[v] = 0
    return 0
S = sr*n+sc
state[S] = 1
if dfs(S, 1):
    print('success')
else: print('fail')
```
