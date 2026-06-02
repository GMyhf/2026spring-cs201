## 2026春数算B-7班期末上机考试（CHENXiang老师班）

*Updated 2026-06-02 19:26 GMT+8*

*Compiled by Hongfei Yan (2026 Spring)*

http://dsa_cx.openjudge.cn/2026final2/

---

### 1: 求阶乘的和

**描述**
给定正整数 n，求不大于 n 的正整数的阶乘的和（即求 1! + 2! + 3! + ... + n!）。

**输入**
输入有一行，包含一个正整数 n（1 < n < 12）。

**输出**
输出有一行：阶乘的和。

**样例输入**
```
5
```

**样例输出**
```
153
```

**思路**
n 的范围很小（1 < n < 12），直接循环计算即可。用变量 `fact` 累计当前阶乘，每轮乘以 i 得到 i!，再加到总和上。

**代码**
```python
n = int(input())
total = 0
fact = 1
for i in range(1, n + 1):
    fact *= i
    total += fact
print(total)
```

---

### 2: 生日相同

**描述**
在一个有 180 人的大班级中，存在两个人生日相同的概率非常大，现给出每个学生的学号，出生月日。试找出所有生日相同的学生。

**输入**
第一行为整数 n，表示有 n 个学生，n < 100。此后每行包含一个字符串和两个整数，分别表示学生的学号（字符串长度小于 10）和出生月 (1 <= m <= 12) 日 (1 <= d <= 31)。学号、月、日之间用一个空格分隔。

**输出**
对每组生日相同的学生，输出一行，其中前两个数字表示月和日，后面跟着所有在当天出生的学生的学号，数字、学号之间都用一个空格分隔。对所有的输出，要求按日期从前到后的顺序输出。对生日相同的学号，按输入的顺序输出。

**样例输入**
```
5
00508192 3 2
00508153 4 5
00508172 3 2
00508023 4 5
00509122 4 5
```

**样例输出**
```
3 2 00508192 00508172
4 5 00508153 00508023 00509122
```

**思路**
用字典将生日 (m, d) 映射到学号列表。按输入顺序依次添加学号。最后按日期升序遍历字典的 key，对学号数 > 1 的组输出。

**代码**
```python
n = int(input())
from collections import OrderedDict
bday_map = {}

for _ in range(n):
    parts = input().split()
    sid = parts[0]
    m, d = int(parts[1]), int(parts[2])
    key = (m, d)
    if key not in bday_map:
        bday_map[key] = []
    bday_map[key].append(sid)

for key in sorted(bday_map.keys()):
    if len(bday_map[key]) > 1:
        print(key[0], key[1], ' '.join(bday_map[key]))
```

---

### 3: 重建二叉树

**描述**
给定一棵二叉树的前序遍历和中序遍历的结果，求其后序遍历。

**输入**
输入可能有多组，以 EOF 结束。每组输入包含两个字符串，分别为树的前序遍历和中序遍历。每个字符串中只包含大写字母且互不重复。

**输出**
对于每组输入，用一行来输出它的后序遍历结果。

**样例输入**
```
DBACEGF ABCDEFG
BCAD CBAD
```

**样例输出**
```
ACBFGED
CDAB
```

**思路**
经典二叉树重建问题。前序的第一个字符是根，在中序中找到根的位置，左边为左子树，右边为右子树。递归处理后序遍历：先左子树、再右子树、最后根。注意多组输入直到 EOF。

**代码**
```python
import sys

def postorder(pre, ino):
    if not pre:
        return ''
    root = pre[0]
    idx = ino.index(root)
    left = postorder(pre[1:1+idx], ino[:idx])
    right = postorder(pre[1+idx:], ino[idx+1:])
    return left + right + root

for line in sys.stdin:
    line = line.strip()
    if not line:
        continue
    pre, ino = line.split()
    print(postorder(pre, ino))
```

---

### 4: 求栈的最大值

**描述**
一个初始为空的栈，每次可以进行以下 3 种操作之一：
1. 指令：`1 x`，表示将正整数 x 压进栈；
2. 指令：`2`，表示如果栈不为空，则栈顶元素出栈；否则忽略该指令；
3. 指令：`3`，表示如果栈不为空，则求栈中元素的最大值；否则输出 0 即可。

给出全部操作序列，对于每个指令 3 输出答案。

**输入**
第一行包含 1 个正整数 n（1 <= n <= 10^6），表示操作指令数。接下去 n 行，每行包含一条指令，其中指令 1 中 1 <= x <= 10^6。

**输出**
对于每个指令 3 输出答案，一个占一行。

**样例输入**
```
11
1 5
3
2
2
3
1 4
3
1 10
3
1 8
3
```

**样例输出**
```
5
0
4
10
10
```

**思路**
用两个栈模拟：`data_stack` 存实际元素，`max_stack` 存当前栈中的最大值。当 push(x) 时，data 栈压入 x，max 栈压入 max(x, max_stack[-1])（若 max 栈空则压 x）。pop 时两栈同时出栈。查询最大值时输出 max_stack[-1]（栈空则输出 0）。n 可达 10^6，用 sys.stdin.buffer 加速读入。

**代码**
```python
import sys

data = sys.stdin.buffer.read().split()
n = int(data[0])
stack = []
max_stack = []
out_lines = []
idx = 1

for _ in range(n):
    op = int(data[idx]); idx += 1
    if op == 1:
        x = int(data[idx]); idx += 1
        stack.append(x)
        if max_stack:
            max_stack.append(x if x > max_stack[-1] else max_stack[-1])
        else:
            max_stack.append(x)
    elif op == 2:
        if stack:
            stack.pop()
            max_stack.pop()
    else:
        if max_stack:
            out_lines.append(str(max_stack[-1]))
        else:
            out_lines.append('0')

sys.stdout.write('\n'.join(out_lines))
```

---

### 5: 糖果

**描述**
Dzx 被赠予糖果公司无限量糖果免费优惠券。糖果公司的 N 件产品每件都包含数量不同的糖果。Dzx 希望他选择的产品包含的糖果总数是 K 的整数倍，这样他才能平均地将糖果分给伙伴们。在满足这一条件的基础上，糖果总数越多越好。注意：Dzx 只能将糖果公司的产品整件带走。

**输入**
第一行包含两个整数 N (1 <= N <= 100) 和 K (1 <= K <= 100)。以下 N 行每行 1 个整数，表示糖果公司该件产品中包含的糖果数目，不超过 1000000。

**输出**
符合要求的最多能达到的糖果总数，如果不能达到 K 的倍数这一要求，输出 0。

**样例输入**
```
5 7
1
2
3
4
5
```

**样例输出**
```
14
```

**思路**
0-1 背包变种。设 `dp[r]` 表示当前余数为 r 时的最大糖果总数。初始 `dp[0] = 0`，其余为 -inf。对每个糖果数 c，新开数组更新：对每个可能的余数 r，若 `dp[r]` 有效，则新余数 `(r + c) % K` 对应值 `new[r] + c` 与旧值取更大。遍历完所有物品后，`dp[0]` 即为答案。

**代码**
```python
n, k = map(int, input().split())
candies = [int(input()) for _ in range(n)]

dp = [-10**18] * k
dp[0] = 0

for c in candies:
    ndp = dp[:]
    for r in range(k):
        if dp[r] >= 0:
            nr = (r + c) % k
            if dp[r] + c > ndp[nr]:
                ndp[nr] = dp[r] + c
    dp = ndp

print(dp[0] if dp[0] > 0 else 0)
```

---

### 6: 动态图连通性

**描述**
有一个 N 个点的无向图，初始没有边。现在执行 Q 次加边操作：每次操作给出两个点 u, v 并在图中加入无向边 (u, v)。你需要在每次操作后输出图中有多少对 x, y 满足 1 <= x <= y <= N，且 x 和 y 连通（即图中存在 x 到 y 的路径）。

**输入**
第一行两个正整数 N, Q，N <= 2 * 10^5, Q <= 2 * 10^5。接下来 Q 行每行两个正整数 u, v，表示加入的边，保证 1 <= u, v <= N。

**输出**
Q 行，每行一个整数，第 i 行表示第 i 次加边后的答案。

**样例输入**
```
3 2
1 2
2 3
```

**样例输出**
```
1
3
```

**思路**
并查集维护连通分量的大小。初始每个点单独一个分量，点对总数 ans = 0。每次加边 (u, v)：
- 若 u 和 v 已在同一分量中，ans 不变。
- 若不在同一分量，设合并前两分量大小分别为 sz1, sz2，合并后 ans 的增加量为 `C(sz1+sz2,2) - C(sz1,2) - C(sz2,2) = sz1 * sz2`（因为 `(sz1+sz2)*(sz1+sz2-1)/2 - sz1*(sz1-1)/2 - sz2*(sz2-1)/2 = sz1*sz2`）。合并两个分量后输出新 ans。

**代码**
```python
import sys

def solve():
    data = sys.stdin.buffer.read().split()
    it = iter(data)
    N = int(next(it))
    Q = int(next(it))

    parent = list(range(N + 1))
    size = [1] * (N + 1)
    ans = 0
    out = []

    def find(x):
        while parent[x] != x:
            parent[x] = parent[parent[x]]
            x = parent[x]
        return x

    for _ in range(Q):
        u = int(next(it)); v = int(next(it))
        ru, rv = find(u), find(v)
        if ru != rv:
            sz1, sz2 = size[ru], size[rv]
            ans += sz1 * sz2
            if size[ru] < size[rv]:
                ru, rv = rv, ru
            parent[rv] = ru
            size[ru] += size[rv]
        out.append(str(ans))

    sys.stdout.write('\n'.join(out))

if __name__ == '__main__':
    solve()
```

---

### 总结

| 题号 | 标题 | 考点 | 难度 | 关键思路 |
|------|------|------|------|----------|
| 1 | 求阶乘的和 | 循环、阶乘 | 入门 | 递推计算阶乘并累加 |
| 2 | 生日相同 | 字典、排序 | 简单 | 以 (月, 日) 为 key 分组，排序后输出 |
| 3 | 重建二叉树 | 二叉树遍历、递归 | 中等 | 前序确定根，中序划分左右子树，递归输出后序 |
| 4 | 求栈的最大值 | 栈、辅助栈 | 中等 | 双栈维护当前最大值，O(1) 查询 |
| 5 | 糖果 | 0-1 背包、模 DP | 中等 | dp[r] 表示余数 r 时最大和，滚动数组优化 |
| 6 | 动态图连通性 | 并查集、按大小合并 | 中等偏难 | 合并时利用 sz1×sz2 增量公式 O(α(N)) 更新 |

