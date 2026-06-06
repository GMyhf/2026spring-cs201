# Cheeting

## Algorithm

### KMP

```python
def kmp(t, p):
    m, n = len(p), len(t)
    if not m:
        return 0
    l = [0] * m
    j = 0
    for i in range(1, m):
        while j and p[i] != p[j]:
            j = l[j - 1]
        if p[i] == p[j]:
            j += 1
            l[i] = j
    j = 0
    for i in range(n):
        while j and t[i] != p[j]:
            j = l[j - 1]
        if t[i] == p[j]:
            j += 1
            if j == m:
                return i - m + 1
    return -1
```

### Shunt

```python
def shunt(e):
    p = {'+': 1, '-': 1, '*': 2, '/': 2}
    o, r, i = [], [], 0
    while i < len(e):
        if e[i].isdigit():
            t = ''
            while i < len(e) and e[i].isdigit():
                t += e[i]
                i += 1
            r.append(t)
            continue
        if e[i] == '(': o.append(e[i])
        elif e[i] == ')':
            while o[-1] != '(':
                r.append(o.pop())
            o.pop()
        elif e[i] in p:
            while o and o[-1] != '(' and p[o[-1]] >= p[e[i]]:
                r.append(o.pop())
            o.append(e[i])
        i += 1
    while o:
        r.append(o.pop())
    return ' '.join(r)
```

### 还原树

```python
def build(pre, ino): # (post, ino)
    d = {v: i for i, v in enumerate(ino)}
    p = 0
    def f(l, r):
        nonlocal p
        if l > r: return None
        v = pre[p] # post[p]
        p += 1 # -= 1
        i = d[v]
        n = Node(v)
        n.l = f(l, i - 1)
        n.r = f(i + 1, r)
        return n
    return f(0, len(ino) - 1)
```



## Data Structure

### LRU Cache

```python
class LRUCache:
    def __init__(self, capacity: int):
        self.capacity = capacity
        self.cache = OrderedDict()
    def get(self, key: int) -> int:
        if key not in self.cache:
            return -1
        self.cache.move_to_end(key, last = False)
        return self.cache[key]
    def put(self, key: int, value: int) -> None:
        self.cache[key] = value
        self.cache.move_to_end(key, last = False)
        if len(self.cache) > self.capacity:
            self.cache.popitem()
```

### DSU

```python
p = list(range(n))
w = [0] * n
def f(x):
    if p[x] != x:
        t = p[x]
        p[x] = f(p[x])
        w[x] += w[t]
    return p[x]

def u(x, y, v):
    a, b = f(x), f(y)
    if a == b:
        return w[x] - w[y] == v
    p[a] = b
    w[a] = v + w[y] - w[x]
    return True
```

### Mono Que

```python
for r, v in enumerate(nums):
    while q and nums[q[-1]] <= v:
        q.pop()
    q.append(r)
    l = r - k + 1
    while q and q[0] < l:
        q.popleft()
    if l >= 0:
        ans.append(nums[q[0]])
```

### ST

```python
class ST:
    def __init__(self, arr, func=min):
        self.func = func
        n = len(arr)
        k = n.bit_length()
        self.log = [0] * (n + 1)
        for i in range(2, n + 1):
            self.log[i] = self.log[i >> 1] + 1

        self.st = [arr[:]]
        for j in range(1, k):
            prev = self.st[j-1]
            self.st.append([
                self.func(prev[i], prev[i + (1 << (j-1))]) for i in range(n - (1 << j) + 1)])

    def query(self, l, r):
        j = self.log[r - l + 1]
        return self.func(self.st[j][l], self.st[j][r - (1 << j) + 1])
```

### BIT

```python
t = [0] * (n + 1)
def update(x, v):
    while x < len(t):
        t[x] += v
        x += x & -x
def s(x):
    r = 0
    while x:
        r += t[x]
        x &= x - 1
    return r

def query(l, r):
    return s(r) - s(l - 1)
```

### Trie

```python
class Trie:
    def __init__(self):
        self.root = {}
    def append(self,s):
        cur=self.root
        for c in s:
            cur=cur.setdefault(c, {})
        cur['#']=True
    def _search(self,s):
        cur=self.root
        for c in s:
            if c not in cur: return 0
            cur=cur[c]
        return 2 if '#' in cur else 1
    def __contains__(self,s):
        return self._search(s)==2
    def startwith(self,s):
        return self._search(s)!=0
```

### SegTree

```python
class SegTree:
    def __init__(self, n):
        self.n = 1
        while self.n < n: self.n *= 2
        self.t = [float('inf')] * (2 * self.n)

    def build(self, a):
        for i in range(len(a)):
            self.t[self.n + i] = a[i]
        for i in range(self.n - 1, 0, -1):
            self.t[i] = min(self.t[i*2], self.t[i*2+1])

    def update(self, x, v):
        x += self.n
        self.t[x] = v
        while x > 1:
            x //= 2
            self.t[x] = min(self.t[x*2], self.t[x*2+1])
    def query(self, l, r):
        s, l, r = 0, l + self.n, r + self.n + 1
        while l < r:
            if l & 1: s += self.t[l]; l += 1
            if r & 1: r -= 1; s += self.t[r]
            l //= 2; r //= 2
        return s

    # min
    def _find(self, x, l, r, ql, qr, v):
        if ql > r or qr < l or self.t[x] > v: return -1
        if l == r: return l
        m = (l + r) // 2
        res = self._find(x*2, l, m, ql, qr, v)
        if res != -1: return res
        return self._find(x*2+1, m+1, r, ql, qr, v)

    def find(self, l, r, v):
        return self._find(1, 0, self.n - 1, l, r, v)

    # 值域sum
    def kth(self, k):
        x = 1
        while x < self.n:
            if self.t[x*2] >= k:
                x = x*2
            else:
                k -= self.t[x*2]
                x = x*2+1
        return x - self.n
```

### LazySegTree

```python
class LazySegTree:
    def __init__(self, n):
        self.n = 1
        while self.n < n: self.n *= 2
        self.t = [0] * (4 * self.n)
        self.d = [0] * (4 * self.n)

    def build(self, a):
        def b(x, l, r):
            if l == r:
                if l < len(a): self.t[x] = a[l]
                return
            m = (l + r) // 2
            b(x*2, l, m)
            b(x*2+1, m+1, r)
            self.t[x] = self.t[x*2] + self.t[x*2+1]
        b(1, 0, self.n - 1)

    def _pd(self, x, l, r):
        if self.d[x] and l != r:
            m = (l + r) // 2
            self.t[x*2] += self.d[x] * (m - l + 1)
            self.t[x*2+1] += self.d[x] * (r - m)
            self.d[x*2] += self.d[x]
            self.d[x*2+1] += self.d[x]
            self.d[x] = 0

    def _up(self, x, l, r, ql, qr, v):
        if ql <= l and r <= qr:
            self.t[x] += v * (r - l + 1)
            self.d[x] += v
            return
        self._pd(x, l, r)
        m = (l + r) // 2
        if ql <= m: self._up(x*2, l, m, ql, qr, v)
        if qr > m: self._up(x*2+1, m+1, r, ql, qr, v)
        self.t[x] = self.t[x*2] + self.t[x*2+1]

    def _qr(self, x, l, r, ql, qr):
        if ql <= l and r <= qr: return self.t[x]
        self._pd(x, l, r)
        m = (l + r) // 2
        s = 0
        if ql <= m: s += self._qr(x*2, l, m, ql, qr)
        if qr > m: s += self._qr(x*2+1, m+1, r, ql, qr)
        return s

    def update(self, l, r, v):
        self._up(1, 0, self.n - 1, l, r, v)

    def query(self, l, r):
        return self._qr(1, 0, self.n - 1, l, r)
```

## Graph

### 朴素Dijkstra

```python
def dijkstra(g, start):
    n = len(g)
    d = [float('inf')] * n
    v = [0] * n
    d[start] = 0
    for _ in range(n):
        x = -1
        for i in range(n):
            if not v[i] and (x == -1 or d[i] < d[x]):
                x = i
        if d[x] == float('inf'): break
        v[x] = 1
        for y, w in g[x]:
            if d[x] + w < d[y]:
                d[y] = d[x] + w
    return d
```

### SPFA

```python
def spfa(g, s):
    n = len(g)
    d = [float('inf')] * n
    iq = [0] * n
    cnt = [0] * n
    d[s] = 0
    q, h, t = [s], 0, 1
    iq[s] = 1
    while h < t:
        x = q[h]
        h += 1
        iq[x] = 0
        for y, w in g[x]:
            if d[x] + w < d[y]:
                d[y] = d[x] + w
                if not iq[y]:
                    q.append(y)
                    t += 1
                    iq[y] = 1
                    cnt[y] += 1
                    if cnt[y] >= n:
                        return False
    return d
```

### Hierholzer

```python
# recru
def dfs(u):
    while g[u]:
        v = g[u].pop()
        dfs(v)
        edges.append([u, v])
    p.append(u)
# stack
while st:
    x = st[-1]
    if g[x]:
        st.append(g[x].pop())
    else:
        p.append(st.pop())
```

### Prim

```python
def prim(g, n):
    v = [0] * n
    d = [float('inf')] * n
    d[0] = 0
    r = 0
    for _ in range(n):
        x = -1
        for i in range(n):
            if not v[i] and (x == -1 or d[i] < d[x]):
                x = i
        if d[x] == float('inf'): break
        v[x] = 1
        r += d[x]
        for y, w in g[x]:
            if not v[y] and w < d[y]:
                d[y] = w
    return r
```

### Boruvka

```python
def boruvka(g, n):
    p = list(range(n))
    def f(x):
        if p[x] != x: p[x] = f(p[x])
        return p[x]
    r, c = 0, n
    while c > 1:
        m = [(-1, float('inf'))] * n
        for x in range(n):
            for y, w in g[x]:
                a, b = f(x), f(y)
                if a != b and w < m[a][1]:
                    m[a] = (b, w)
        for a in range(n):
            b, w = m[a]
            if b != -1 and f(a) != f(b):
                p[f(a)] = f(b)
                r += w
                c -= 1
    return r
```

### Tarjan

```python
def tarjan_scc(g, n):
    d, l, s, iq, idx, r = [0] * n, [0] * n, [], [False] * n, 0, []
    def f(u): # f(u, fa)
        nonlocal idx
        idx += 1
        d[u] = l[u] = idx
        s.append(u); iq[u] = True
        for v in g[u]:
            if not d[v]:
                # cut: ch += 1
                f(v)
                l[u] = min(l[u], l[v])
                # cut: if p != -1 and l[v] >= d[u]: c[u] = True
                # bridge: if l[v] > d[u]: b.append((u, v))
            elif iq[v]:
                l[u] = min(l[u], d[v])
        # cut: if p == -1 and ch > 1: c[u] = True
        if d[u] == l[u]:
            c = []
            while True:
                x = s.pop(); iq[x] = False; c.append(x)
                if x == u: break
            r.append(c)
    for i in range(n):
        if not d[i]: f(i) # f(i, -1)
    return r
```

### 树上背包

```python
def knap(g, V, W, C, r):
    n = len(g)
    dp = [[0] * (C + 1) for _ in range(n)]
    def f(u, p):
        for i in range(W[u], C + 1):
            dp[u][i] = V[u]
        for v in g[u]:
            if v == p: continue
            f(v, u)
            for i in range(C, W[u] - 1, -1):
                for j in range(1, i - W[u] + 1):
                    dp[u][i] = max(dp[u][i], dp[u][i - j] + dp[v][j])
    f(r, -1)
    return max(dp[r])
```

### 补图BFS

```python
from collections import deque
def comp_cc(g, n):
    nxt = [i + 1 for i in range(n)]
    nxt[-1] = -1
    pre = [-1] + list(range(n - 1))
    head = 0
    def erase(x):
        nonlocal head
        if pre[x] != -1: nxt[pre[x]] = nxt[x]
        if nxt[x] != -1: pre[nxt[x]] = pre[x]
        if x == head: head = nxt[x]
    mark = [0] * n
    ans = 0
    while head != -1:
        ans += 1
        s = head
        erase(s)
        q = deque([s])
        while q:
            u = q.popleft()
            for v in g[u]: mark[v] = 1
            p = head
            while p != -1:
                np = nxt[p]
                if not mark[p]:
                    erase(p)
                    q.append(p)
                p = np
            for v in g[u]: mark[v] = 0
    return ans
```

