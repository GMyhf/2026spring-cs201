## **2026 数算B模拟考试 gw班**

*Updated 2026-05-28 12:46 GMT+8* 

*Compiled by Hongfei Yan (2026 Spring)*



| #    | 题目             | 算法                               |
| ---- | ---------------- | ---------------------------------- |
| 001  | 最近点对         | 暴力枚举 O(N²)                     |
| 002  | 最大括号嵌套深度 | 栈匹配括号                         |
| 003  | 终极排队         | 并查集                             |
| 004  | 删除链表重复元素 | 遍历 + Set 判重                    |
| 005  | 派               | 二分答案                           |
| 006  | 简单建树         | 字典建树 + 前序递归                |
| 007  | 感染爆发         | BFS 模拟传播                       |
| 008  | 最少要建多少条路 | 并查集                             |
| 009  | 最优爬山路径     | 二分 H + BFS 检验                  |
| 010  | 邮递员送快递     | 正向/反向图 Dijkstra               |
| 011  | 最大连续答案     | 滑动窗口                           |
| 012  | 多少人知道秘密   | DP 递推                            |
| 013  | 累加树           | 构建 BST + 右-根-左累加 + BFS 输出 |



## 001:最近点对

brute force, http://dsbpython.openjudge.cn/2026moni1re/001/

> 给定一系列二维空间的点坐标，求距离最近的两个点之间的距离的平方。 两点间的距离的平方为 `(x1-x2)**2+(y1-y2)**2`。 (x**y表示x的二次方)
>
> **输入**
>
> 第一行 T,表示有T组数据
>
> 对于每组数据：
>
> 第一行N，表示N个点。
> 接下来N行，每一行两个整数，表示该点的x,y坐标。
>
> **输出**
>
> 对每组数据，输出一个整数，最近点对间的距离的平方。
>
> 样例输入
>
> ```
> 2
> 3
> 1 1
> 0 0 
> 2 1
> 4
> 1 4
> 3 5
> 2 7
> 9 10
> ```
>
> 样例输出
>
> ```
> 1
> 5
> ```



**思路**：暴力枚举所有点对，计算距离平方，取最小值。对于 N 个点，枚举所有 (i,j) 对即可，时间复杂度 O(N²)。

```python
def solve():
    data = list(map(int, sys.stdin.read().split()))
    it = iter(data)
    T = next(it)
    out = []
    for _ in range(T):
        n = next(it)
        pts = [(next(it), next(it)) for _ in range(n)]
        ans = float('inf')
        for i in range(n):
            xi, yi = pts[i]
            for j in range(i + 1, n):
                xj, yj = pts[j]
                d = (xi - xj) ** 2 + (yi - yj) ** 2
                if d < ans:
                    ans = d
        out.append(str(ans))
    sys.stdout.write("\n".join(out))

if __name__ == "__main__":
    import sys
    solve()
```



## 002:最大括号嵌套深度

stack, http://dsbpython.openjudge.cn/2026moni1re/002/

> 字符串中有括号。括号可能有3种成对的，"( )"、"[ ]"、"{}"。请判断字符串的括号是否都正确配对以及最大括号嵌套深度。括号交叉算不正确配对，例如"1234[78)ab]"就不算正确配对。一对括号被包含在另一对括号里面，例如"12(ab[8])"就算括号嵌套。括号嵌套不影响配对的正确性。 给定一个字符串: 如果括号没有正确配对，则输出 "ERROR" 如果正确配对了，且有括号嵌套现象，则输出括号的最大嵌套深度。如果正确配对了，但是没有括号嵌套现象，则输出"0"。
> 括号最大嵌套深度是括号匹配过程中同时未被匹配的左括号的最大数量（但这个数量如果是1就认为没有括号嵌套现象）。
>
> **输入**
>
> 一个字符串，不包括含空格。
>
> **输出**
>
> ERROR或0或最大括号嵌套深度
>
> 样例输入
>
> ```
> 样例1:
> [](){}
> 样例2:
> [(a)]bv[]
> 样例3:
> [[(])]{}
> ```
>
> 样例输出
>
> ```
> 样例1:
> 0
> 样例2:
> 2
> 样例3:
> ERROR
> ```



**思路**：用栈匹配括号。左括号入栈，右括号时检查栈顶是否匹配。同时维护栈的当前深度（即当前栈大小），取最大值。若匹配失败输出 ERROR；最大深度≤1 输出 0，否则输出最大深度。

```python
def solve():
    s = sys.stdin.readline().strip()
    pair = {')': '(', ']': '[', '}': '{'}
    stack = []
    max_depth = 0
    for ch in s:
        if ch in '([{':
            stack.append(ch)
            max_depth = max(max_depth, len(stack))
        elif ch in ')]}':
            if not stack or stack[-1] != pair[ch]:
                print("ERROR")
                return
            stack.pop()
    if stack:
        print("ERROR")
    elif max_depth <= 1:
        print(0)
    else:
        print(max_depth)

if __name__ == "__main__":
    import sys
    solve()
```



## 003:终极排队

DSU, http://dsbpython.openjudge.cn/2026moni1re/003/

> 操场上有好多好多同学在玩耍，体育老师冲了过来，要求他们排队。同学们纪律实在太散漫了，老师不得不来手动整队：
> "A，你站在B的后面。" 
> "C，你站在D的后面。" 
> "B，你站在D的后面。哦，去D队伍的最后面。"
>
> 更形式化地，初始时刻，操场上有 n 位同学，自成一列。每次操作，老师的指令是 "x y"，表示 x 所在的队列排到 y 所在的队列的后面，即 x 的队首排在 y 的队尾的后面。（如果 x 与 y 已经在同一队列，请忽略该指令） 最终的队列数量远远小于 n，老师很满意。请你输出最终时刻每位同学所在队列的队首（排头），老师想记录每位同学的排头，方便找人。而且还要输出每位同学所在的队的人数。
>
> **输入**
>
> 第一行一个整数 T (T≤5)，表示测试数据组数。 接下来 T 组测试数据，对于每组数据，第一行两个整数 n 和 m (n,m≤30000)，紧跟着 m 行每行两个整数 
> x 和 y (1≤x,y≤n)。
>
> **输出**
>
> 对每组数据输出2行。 
> 第1行是n 个整数，表示每位同学的排头 
> 第2行是n 个整数，表示每位同学所在的队的人数
>
> 样例输入
>
> ```
> 2
> 4 2
> 1 2
> 3 4
> 5 4
> 1 2
> 2 3
> 4 5
> 1 3
> ```
>
> 样例输出
>
> ```
> 2 2 4 4
> 2 2 2 2
> 3 3 3 5 5
> 3 3 3 2 2
> ```
>
> 



这是一个经典的并查集（Disjoint Set Union, DSU）应用问题。

### 解题思路

1. **队列的合并与代表元**：
   - 初始时，每个同学自成一列。我们可以用并查集来维护每个队列。
   - 每个队列的“队首”（排头）可以作为该并查集的“代表元”（即根节点）。
   - 当执行指令 `"x y"` 时，意味着将 $x$ 所在的队列整体排到 $y$ 所在的队列后面。合并后，新队列的队首显然就是原 $y$ 队列的队首。
   - 因此，在并查集中，我们将 $x$ 所在集合的根节点指向 $y$ 所在集合的根节点（即 `parent[root_x] = root_y`）。

2. **维护队列人数**：
   - 我们用一个数组 `size` 来记录以每个节点为根的集合（队列）的人数。
   - 在合并时，新队列的人数等于两个旧队列人数之和，即 `size[root_y] += size[root_x]`。

3. **路径压缩**：
   - 为了保证查询的效率，在执行 `find` 操作时使用路径压缩。采用非递归（迭代）的方式实现 `find` 函数，不仅运行速度快，还可以有效避免 Python 的递归深度限制。

### Python 3 源代码

```python
import sys

def solve():
    # 使用快速输入读取所有数据
    input_data = sys.stdin.read().split()
    if not input_data:
        return
    
    T = int(input_data[0])
    idx = 1
    
    out = []
    for _ in range(T):
        if idx >= len(input_data):
            break
        n = int(input_data[idx])
        m = int(input_data[idx+1])
        idx += 2
        
        # 初始化并查集
        # parent[i] 表示 i 的上级，初始时指向自己
        parent = list(range(n + 1))
        # size[i] 表示以 i 为队首的队伍人数
        size = [1] * (n + 1)
        
        # 迭代版 find 函数（带路径压缩）
        def find(i):
            curr = i
            while parent[curr] != curr:
                curr = parent[curr]
            # 路径压缩
            node = i
            while node != curr:
                nxt = parent[node]
                parent[node] = curr
                node = nxt
            return curr

        # 处理 m 条指令
        for _ in range(m):
            x = int(input_data[idx])
            y = int(input_data[idx+1])
            idx += 2
            
            rx = find(x)
            ry = find(y)
            if rx != ry:
                # x 所在的队列排到 y 所在的队列后面
                # 合并后的新队首为 y 所在的队列的队首 ry
                parent[rx] = ry
                size[ry] += size[rx]
                
        # 统计结果
        heads = []
        sizes = []
        for i in range(1, n + 1):
            root = find(i)
            heads.append(str(root))
            sizes.append(str(size[root]))
            
        out.append(" ".join(heads))
        out.append(" ".join(sizes))
        
    # 打印最终结果
    print("\n".join(out))

if __name__ == '__main__':
    solve()
```

### 复杂度分析

- **时间复杂度**：对于每组测试数据，初始化时间为 $O(n)$。每次 `find` 操作和合并操作的时间复杂度在路径压缩下接近 $O(1)$（严格来说是反阿克曼函数 $\alpha(n)$）。因此，总时间复杂度为 $O(T \times (n + m))$，可以高效地在时限内通过。
- **空间复杂度**：主要消耗在存储父节点和队伍大小的数组上，空间复杂度为 $O(n)$。



## 004:删除链表重复元素

http://dsbpython.openjudge.cn/2026moni1re/004/

> 程序填空，对链表中的重复元素，只保留第1个，然后输出删除后的结果
>
> ```python
> class ListNode:
>     def __init__(self, val=0, next=None):
>         self.val = val
>         self.next = next
> 
> def build_linked_list(values):
>     dummy = ListNode(0)
>     current = dummy
>     for val in values:
>         current.next = ListNode(val)
>         current = current.next
>     return dummy.next
> 
> def print_linked_list(head):
>     current = head
>     result = []
>     while current:
>         result.append(str(current.val))
>         current = current.next
>     print(" ".join(result) if result else "None")
> 
> def deleteDuplicates(head):
> // 在此处补充你的代码
> 
> def main():
>     values = list(map(int,input().split()))
>     head = build_linked_list(values)
>     deleteDuplicates(head)
>     print_linked_list(head)
> main()
> ```
>
> **输入**
>
> 原始链表的数据
>
> **输出**
>
> 去除重复后的链表的数据
>
> 样例输入
>
> `1 2 3 2 3 3 1 `
>
> 样例输出
>
> `1 2 3`



**思路**：遍历链表，用 set 记录已出现的值。遇到重复节点时跳过（将 prev.next 指向 curr.next），否则将值加入 set 并移动 prev。

```python
def deleteDuplicates(head):
    if not head:
        return head
    seen = {head.val}
    prev, curr = head, head.next
    while curr:
        if curr.val in seen:
            prev.next = curr.next
        else:
            seen.add(curr.val)
            prev = curr
        curr = curr.next
```



## 005:派

binary search, http://dsbpython.openjudge.cn/2026moni1re/005/

> 我的生日要到了！根据习俗，我需要将一些派分给大家。我有N个不同口味、不同大小的派。有F个朋友会来参加我的派对，每个人会拿到一块派（必须一个派的一块，不能由几个派的小块拼成；可以是一整个派）。
>
> 我的朋友们都特别小气，如果有人拿到更大的一块，就会开始抱怨。因此所有人拿到的派是同样大小的（但不需要是同样形状的），虽然这样有些派会被浪费，但总比搞砸整个派对好。当然，我也要给自己留一块，而这一块也要和其他人的同样大小。
>
> 请问我们每个人拿到的派最大是多少？每个派都是一个高为1，半径不等的圆柱体。
>
> **输入**
>
> 第一行包含两个正整数N和F，1 ≤ N, F ≤ 10 000，表示派的数量和朋友的数量。 第二行包含N个1到10000之间的整数，表示每个派的半径。
>
> **输出**
>
> 输出每个人能得到的最大的派的体积，精确到小数点后三位。
>
> 样例输入
>
> ```
> 3 3
> 4 3 3
> ```
>
> 样例输出
>
> `25.133`



这是一个经典的**二分答案（Binary Search on Answer）**问题。

### 解题思路

1. **题目分析**：
   * 有 $N$ 个派，每个派的高度为 $1$，半径为 $r_i$。因此，每个派的体积为 $V_i = \pi \cdot r_i^2 \cdot 1 = \pi \cdot r_i^2$。
   * 需要分给 $F + 1$ 个人（$F$ 个朋友加上自己）。
   * 每个人分到的派必须是完整的一块（不能由多个小块拼接而成），且所有人的派大小相同。
   * 我们需要求每个人能分到的**最大体积**。

2. **二分查找**：
   * 每个人分到的派体积的范围在 $[0, \max(V_i)]$ 之间。
   * 这是一个单调性问题：如果每个人分到体积为 $x$ 的派是可行的，那么小于 $x$ 的体积一定可行；如果体积 $x$ 不可行，那么大于 $x$ 的体积也一定不可行。
   * 因此，我们可以对分到的体积 $x$ 进行二分查找：
     * 如果以 $x$ 为大小，能切出的派的块数 $\ge F + 1$，说明 $x$ 可能还可以更大，我们将左边界更新为 $x$。
     * 否则，说明 $x$ 太大了，切不出足够的块数，我们将右边界更新为 $x$。
   * 由于是浮点数二分，通常迭代 100 次左右即可达到极高的精度。

### Python 3 代码实现

```python
import math
import sys


def solve():
    # 读取所有输入
    input_data = sys.stdin.read().split()
    if not input_data:
        return

    N = int(input_data[0])
    F = int(input_data[1])
    radii = [float(x) for x in input_data[2 : 2 + N]]

    # 总人数（朋友数量 + 自己）
    total_people = F + 1

    # 计算每个派的体积 (V = pi * r^2)
    volumes = [math.pi * (r**2) for r in radii]

    # 二分查找的左右边界
    low = 0.0
    high = max(volumes)

    # 迭代100次以确保精度
    for _ in range(100):
        mid = (low + high) / 2

        # 计算以 mid 为体积时，总共可以切出多少块
        pieces = sum(int(v / mid) for v in volumes)

        if pieces >= total_people:
            low = mid  # 尝试更大的体积
        else:
            high = mid  # 体积太大，需要缩小

    # 打印结果，保留三位小数
    print(f"{low:.3f}")


if __name__ == "__main__":
    solve()
```

### 复杂度分析

* **时间复杂度**：$O(N \log(\text{precision}))$。每次二分计算需要遍历 $N$ 个派，迭代 100 次，因此总计算量约为 $100 \times N$。当 $N = 10,000$ 时，计算量在 $10^6$ 级别，运行时间通常在 0.1 秒内，完全满足时限要求。
* **空间复杂度**：$O(N)$，用于存储每个派的半径和体积。



## 006:简单建树

tree, http://dsbpython.openjudge.cn/2026moni1re/006/

> 输入二叉树，输出其前序遍历序列
>
> **输入**
>
> 第一行为二叉树的结点数 n。( 1 <= 1 <=26)
> 后面 n 行，每一个字母为结点，后两个字母分别为其左右儿子。
> 空结点用 * 表示
>
> **输出**
>
> 二叉树的前序遍历序列
>
> 样例输入
>
> ```
> 6
> abc
> bdi
> cj*
> d**
> i**
> j**
> ```
>
> 样例输出
>
> ```
> abdicj
> ```



**思路**：用字典存储每个节点的左右孩子，再找出根节点（没有作为子节点出现的节点）。从根开始递归前序遍历（根-左-右）。

```python
def solve():
    n = int(sys.stdin.readline())
    children = {}
    all_nodes = set()
    child_set = set()
    for _ in range(n):
        line = sys.stdin.readline().strip()
        node, left, right = line[0], line[1], line[2]
        children[node] = (left, right)
        all_nodes.add(node)
        if left != '*':
            child_set.add(left)
        if right != '*':
            child_set.add(right)
    root = (all_nodes - child_set).pop()

    out = []
    def preorder(u):
        if u == '*':
            return
        out.append(u)
        preorder(children[u][0])
        preorder(children[u][1])
    preorder(root)
    print("".join(out))

if __name__ == "__main__":
    import sys
    solve()
```



## 007:感染爆发

bfs, http://dsbpython.openjudge.cn/2026moni1re/007/

> 学校同学之间爆发了一种流感，导致同学们有很多没有来到教室上课。一旦被感染，流感就会向自己的周围人传播。假设同学们处在一个 M x N 的网格中，初始时有若干名同学感染，每过去一天，感染的同学就会将流感传染给上下左右相邻的健康同学。请问过去 k 天后，共有多少人感染流感？
>
> **输入**
>
> 第一行是两个整数 M 和 N，用空格隔开，代表同学所在网格的大小（ 2 <= M, N <= 100）。
> 接下来 M 行代表同学网格，每一行有 N 个元素，为 0 代表一个健康同学，为 1 代表一个感染同学的。
> 最后一行是一个整数 k（ 1 <= 1 <= 100）。
>
> **输出**
>
> k 天后感染流感的人数。
>
> 样例输入
>
> ```
> 4 5
> 01000
> 10000
> 00010
> 00001
> 2
> ```
>
> 样例输出
>
> ```
> 18
> ```



**思路**：BFS 模拟感染过程。先将初始感染者入队，每天传播到上下左右四个方向，若该位置健康则感染并入队。模拟 k 天，统计最终感染人数。

```python
from collections import deque

def solve():
    data = sys.stdin.read().splitlines()
    if not data:
        return
    m, n = map(int, data[0].split())
    grid = [list(line.strip()) for line in data[1:1+m]]
    k = int(data[1+m])
    q = deque()
    for i in range(m):
        for j in range(n):
            if grid[i][j] == '1':
                q.append((i, j))
    dirs = [(0,1),(0,-1),(1,0),(-1,0)]
    for _ in range(k):
        if not q:
            break
        for _ in range(len(q)):
            x, y = q.popleft()
            for dx, dy in dirs:
                nx, ny = x+dx, y+dy
                if 0 <= nx < m and 0 <= ny < n and grid[nx][ny] == '0':
                    grid[nx][ny] = '1'
                    q.append((nx, ny))
    cnt = sum(row.count('1') for row in grid)
    print(cnt)

if __name__ == "__main__":
    import sys
    solve()
```



## 008:最少要建多少条路

DSU, http://dsbpython.openjudge.cn/2026moni1re/008/

> 某省调查城镇交通状况，得到现有城镇道路统计表，表中列出了每条道路直接连通的城镇。省政府“畅通工程”的目标是使全省任何两个城镇间都可以实现交通（但不一定有直接的道路相连，只要互相间接通过道路可达即可）。问最少还需要建设多少条道路？
>
> **输入**
>
> 第 1 行给出两个正整数，分别是城镇数目 N ( N < 1000 )和道路数目 M；
> 随后的 M 行对应 M 条道路，每行给出一对正整数，分别是该条道路直接连通的两个城镇的编号。为简单起见，城镇从 1 到 N 编号。
> 注意:两个城市之间可以有多条道路相通,也就是说
> 3 3
> 1 2
> 1 2
> 2 1
> 这种输入也是合法的
>
> **输出**
>
> 输出最少还需要建设的道路数目,一个整数。
>
> 样例输入
>
> ```
> 4 2
> 1 3
> 4 3
> ```
>
> 样例输出
>
> ```
> 1
> ```



**思路**：并查集统计连通分量个数。初始每个城镇自成一派，每读入一条已存在的路就合并。最终连通分量个数减 1 即为最少需要建设的道路数。

```python
def solve():
    data = list(map(int, sys.stdin.read().split()))
    n, m = data[0], data[1]
    parent = list(range(n + 1))
    def find(x):
        while parent[x] != x:
            parent[x] = parent[parent[x]]
            x = parent[x]
        return x
    def union(a, b):
        ra, rb = find(a), find(b)
        if ra != rb:
            parent[ra] = rb
    idx = 2
    for _ in range(m):
        u, v = data[idx], data[idx+1]
        union(u, v)
        idx += 2
    roots = {find(i) for i in range(1, n+1)}
    print(len(roots) - 1)

if __name__ == "__main__":
    import sys
    solve()
```



## M30909: 最优爬山路径

binary search, bfs, http://cs101.openjudge.cn/practice/30909/

> 小P要从一块山地的一头走到另一头。山地可以看作是一个由n×m个单元构成的网格，每个单元有一个高度值。
> 小P要从左上角的单元走到右下角的单元。小P只能沿着东西南北四个方向走。小P有恐高症，又讨厌爬升，所以他想要找一条路，使得路上高度差的绝对值最大的相邻两个单元格的高度差的绝对值H尽可能小。问H最小可以是多少。
>
> **输入**
>
> 第1行是整数n和m,表示山地是一个n×m的网格( 1 < n,m <= 100)
> 接下来有n行，每行有m个整数，描述了山地中所有单元的高度h。 （0 <= h <= 10000)
>
> **输出**
>
> 最小的H
>
> 样例输入
>
> ```
> 3 3
> 1 3 8
> 2 1 5
> 4 6 3
> ```
>
> 样例输出
>
> ```
> 3
> ```



这个问题可以通过**二分查找（Binary Search）结合广度优先搜索（BFS）**来解决。

**解题思路**

1. **二分查找高度差 $H$：**
   * 最小可能的高度差 $H$ 为 $0$，最大可能的高度差为网格中最大高度与最小高度之差（不超过 $10000$）。
   * 我们可以对高度差 $H$ 的范围进行二分查找。

2. **BFS 验证可行性：**
   * 对于每次二分选择的中间值 `mid`，我们需要判断是否存在一条从左上角 `(0, 0)` 到右下角 `(n-1, m-1)` 的路径，使得路径上任意相邻两个单元格的高度差的绝对值都不超过 `mid`。
   * 这个验证过程可以使用 BFS（或 DFS）来实现。我们从 `(0, 0)` 开始，只向高度差不超过 `mid` 且未访问过的相邻格子移动。如果最终能到达 `(n-1, m-1)`，说明当前的 `mid` 是可行的。

3. **调整二分区间：**
   * 如果当前 `mid` 可行，尝试寻找更小的 $H$，即在左半区间继续查找（`high = mid - 1`），并记录当前可行解。
   * 如果不可行，说明限制太小，需要增加 $H$，在右半区间继续查找（`low = mid + 1`）。

**Python 3 实现代码**

```python
import sys
from collections import deque


def solve():
    # 读取所有输入数据
    input_data = sys.stdin.read().split()
    if not input_data:
        return

    n = int(input_data[0])
    m = int(input_data[1])

    grid = []
    idx = 2
    for _ in range(n):
        grid.append([int(x) for x in input_data[idx : idx + m]])
        idx += m

    # BFS 函数：验证在最大高度差限制为 H 的情况下，能否从起点走到终点
    def can_reach(H):
        visited = [[False] * m for _ in range(n)]
        queue = deque([(0, 0)])
        visited[0][0] = True

        # 四个移动方向：上下左右
        directions = [(-1, 0), (1, 0), (0, -1), (0, 1)]

        while queue:
            r, c = queue.popleft()

            # 成功到达终点
            if r == n - 1 and c == m - 1:
                return True

            for dr, dc in directions:
                nr, nc = r + dr, c + dc
                if 0 <= nr < n and 0 <= nc < m:
                    if not visited[nr][nc]:
                        # 检查高度差是否在限制 H 以内
                        if abs(grid[r][c] - grid[nr][nc]) <= H:
                            visited[nr][nc] = True
                            queue.append((nr, nc))
        return False

    # 确定二分查找的上下边界
    flat_grid = [val for row in grid for val in row]
    min_val = min(flat_grid)
    max_val = max(flat_grid)

    low = 0
    high = max_val - min_val
    ans = high

    # 二分查找
    while low <= high:
        mid = (low + high) // 2
        if can_reach(mid):
            ans = mid
            high = mid - 1  # 尝试寻找更小的可行高度差
        else:
            low = mid + 1  # 限制太小，增加高度差

    print(ans)


if __name__ == "__main__":
    solve()
```

**复杂度分析**

* **时间复杂度：** 
  二分查找的范围为 $[0, 10000]$，最多需要进行 $\log_2(10000) \approx 14$ 次迭代。在每次迭代中，BFS 最多遍历网格中的所有节点和边，时间复杂度为 $O(V + E)$，其中节点数 $V = n \times m \le 10000$，边数 $E \approx 4 \times n \times m$。因此，整体时间复杂度为 $O(n \times m \log(\max\_height))$。对于 $100 \times 100$ 的网格，运算次数大约为 $14 \times 10000 \approx 1.4 \times 10^5$，在 Python 中可以在 100ms 内轻松完成，远低于 1000ms 的限制。

* **空间复杂度：** 
  主要开销为存储网格的 `grid` 数组、BFS 使用的 `visited` 数组以及队列，最大空间占用大约为 $O(n \times m)$。在 $100 \times 100$ 规模下，占用内存极小，安全符合 65536kB 的内存限制。



## M30910: 邮递员送快递

Dijkstra, http://cs101.openjudge.cn/practice/30910/

> 某县有n个村庄，由一个邮递员负责送快递。村庄编号1到n。邮局在村庄 1。他总共要送 n-1 样东西，其目的地分别是村庄 2 到村庄 n。
> 由于这个县地方小且交通比较繁忙，因此所有的道路都是单行的，共有 m 条道路，每条道路直接连接两个村庄。这个邮递员每次只能带一样东西，并且运送每件物品过后必须返回邮局。求送完这 n-1 样东西并且最终回到邮局最少需要的时间。
>
> **输入**
>
> 第一行包括两个整数，n 和 m，表示村庄的村庄数量和道路数量。(n 不超过 1100,m 不超过 100000）
>
> 接下来的m行，每行三个整数，u,v,w，表示从村庄 u 到村庄 v 有一条通过时间为 w 的道路
>
> **输出**
>
> 输出仅一行，包含一个整数，为最少需要的时间。
>
> 样例输入
>
> ```
> 5 10
> 2 3 5
> 1 5 5
> 3 5 6
> 1 2 8
> 1 3 8
> 5 3 4
> 4 1 8
> 4 5 3
> 3 5 6
> 5 4 2
> ```
>
> 样例输出
>
> ```
> 83
> ```



**思路**：从邮局（村庄 1）出发，需要到村庄 2..n 各送一次，每次送完返回邮局。总耗时 = Σ(1→i 最短路径 + i→1 最短路径)。用 Dijkstra 在正向图上求 1 到所有点的最短路，再在反向图（边方向相反）上求 1 到所有点的最短路，后者即为各点到 1 的最短路。最后求和。

```python
import heapq

def solve():
    data = list(map(int, sys.stdin.read().split()))
    it = iter(data)
    n = next(it); m = next(it)
    g = [[] for _ in range(n + 1)]
    rg = [[] for _ in range(n + 1)]
    for _ in range(m):
        u = next(it); v = next(it); w = next(it)
        g[u].append((v, w))
        rg[v].append((u, w))

    def dijkstra(graph, start):
        dist = [float('inf')] * (n + 1)
        dist[start] = 0
        pq = [(0, start)]
        while pq:
            d, u = heapq.heappop(pq)
            if d > dist[u]:
                continue
            for v, w in graph[u]:
                nd = d + w
                if nd < dist[v]:
                    dist[v] = nd
                    heapq.heappush(pq, (nd, v))
        return dist

    d1 = dijkstra(g, 1)
    d2 = dijkstra(rg, 1)
    ans = sum(d1[i] + d2[i] for i in range(2, n + 1))
    print(ans)

if __name__ == "__main__":
    import sys
    solve()
```



## M30942: 最大连续答案

sliding window, http://cs101.openjudge.cn/practice/30942/

> 给定一个长度不超过20000的由'T'或'F'构成的字符串，允许修改字符最多k次（将'T'改成'F'或将'F'改成'T')，问能得到的最长的连续相同字符字串长度是多少。
>
> **输入**
>
> 第1行，一个由'T'或'F'构成的字符串
> 第2行：整数k (0 <= k <= 第1行字符串长度）
>
> **输出**
>
> 答案
>
> 样例输入
>
> ```
> TTFF
> 2
> ```
>
> 样例输出
>
> ```
> 4
> ```



**思路**：滑动窗口。分别将 'T' 和 'F' 视为目标字符，用双指针维护窗口内最多允许 k 个非目标字符。每一次移动右指针扩大窗口，若窗口内非目标字符超过 k 则移动左指针收缩。记录窗口最大长度。

```python
def solve():
    s = sys.stdin.readline().strip()
    k = int(sys.stdin.readline())
    ans = 0
    for target in ('T', 'F'):
        l = cnt = 0
        for r in range(len(s)):
            if s[r] != target:
                cnt += 1
            while cnt > k:
                if s[l] != target:
                    cnt -= 1
                l += 1
            ans = max(ans, r - l + 1)
    print(ans)

if __name__ == "__main__":
    import sys
    solve()
```



## T30911: 多少人知道秘密

dp, http://cs101.openjudge.cn/practice/30911/

> 在第 1 天，有一个人发现了一个秘密。
> 给你一个整数 delay ，表示每个人会在发现秘密后的 第delay 天及以后，每天将秘密告诉给一个新人。同时给你一个整数 forget ，表示每个人在发现秘密的后的第 forget 天及之后会忘记这个秘密。一个人不能 在忘记秘密那一天及之后的日子里把秘密告诉别人。
>
> 给你一个整数 n ，请计算第 n 天结束时，知道秘密的人数。由于答案可能会很大，请你将结果对
> 1000000007取余后输出。
>
> **输入**
>
> 一行，包括三个整数n,delay和forget ( 1 <= n <= 100)
>
> **输出**
>
> 第 n 天结束时，知道秘密的人数对1000000007取余后的结果。
>
> 样例输入
>
> ```
> #输入样例1
> 4 1 3
> #输入样例2
> 90 3 9
> ```
>
> 样例输出
>
> ```
> #输出样例1
> 6
> #输出样例2
> 386701165
> ```
>
> 提示
>
> 对样例1的解释：
> 第 1 天：第一个知道秘密的人为 A 。（一个人知道秘密）
> 第 2 天：A 把秘密分享给 B 。（两个人知道秘密）
> 第 3 天：A 和 B 把秘密分享给 2 个新的人 C 和 D 。（四个人知道秘密）
> 第 4 天：A 忘记了秘密，B、C、D 分别分享给 3 个新的人。（六个人知道秘密）



这是一个经典的动态规划（Dynamic Programming）问题。可以通过维护每天“新知道秘密的人数”来解决。

**算法思路**

设 `dp[i]` 表示在第 `i` 天**新发现/得知**秘密的人数。

1. **初始状态**：第 1 天只有 1 个人知道秘密，所以 `dp[1] = 1`。
2. **状态转移**：对于第 `i` 天（$i > 1$），新得知秘密的人是由之前已经知道秘密、且处于“可以分享秘密”阶段的人分享而来的。
   * 一个人在第 `j` 天得知秘密后，他能够分享秘密的区间是：从第 `j + delay` 天开始，到第 `j + forget - 1` 天结束。
   * 反过来，在第 `i` 天，能够分享秘密的人必须是在第 `j` 天得知秘密的，其中满足：$i - \text{forget} < j \le i - \text{delay}$。
   * 因此，第 `i` 天新得知秘密的人数：
     $$dp[i] = \sum_{j = \max(1, i - \text{forget} + 1)}^{i - \text{delay}} dp[j]$$
3. **计算第 `n` 天结束时知道秘密的总人数**：
   * 只有在第 `j` 天得知秘密，且到第 `n` 天还没有忘记的人才算在内。
   * 满足未忘记的条件是：$j + \text{forget} > n \implies j > n - \text{forget}$。
   * 所以最终人数为：
     $$\text{Total} = \sum_{j = \max(1, n - \text{forget} + 1)}^{n} dp[j]$$
   * 每次累加时需要对 $1000000007$ 取模。

**Python 3 实现代码**

```python
import sys

def solve():
    # 读取所有输入
    input_data = sys.stdin.read().split()
    if not input_data:
        return
    
    n = int(input_data[0])
    delay = int(input_data[1])
    forget = int(input_data[2])
    
    MOD = 1000000007
    
    # dp[i] 表示第 i 天新知道秘密的人数
    dp = [0] * (n + 1)
    dp[1] = 1
    
    for i in range(2, n + 1):
        # 能在第 i 天分享秘密的人，其得知秘密的日期 j 需满足：
        # i - forget < j <= i - delay
        start = max(1, i - forget + 1)
        end = i - delay
        
        current_shares = 0
        # # 如果 start > end，这里会自动跳过，不影响结果
        for j in range(start, end + 1):
            current_shares = (current_shares + dp[j]) % MOD
        dp[i] = current_shares
        
    # 计算在第 n 天结束时，还没忘记秘密的人数之和
    # 还没忘记秘密的人，其得知秘密的日期 j 需满足：
    # j + forget > n  =>  j >= n - forget + 1
    total_knowing = 0
    start = max(1, n - forget + 1)
    for j in range(start, n + 1):
        total_knowing = (total_knowing + dp[j]) % MOD
        
    print(total_knowing)

if __name__ == '__main__':
    solve()
```

**复杂度分析**

- **时间复杂度**：对于每一天 $i$，我们需要累加长度最多为 $\text{forget}$ 的区间。由于 $n \le 100$，双重循环的最大计算量约为 $100 \times 100 = 10^4$ 次操作，运行时间远低于 1000ms 限制。
- **空间复杂度**：使用了大小为 $n + 1$ 的 `dp` 数组，空间复杂度为 $O(n)$，在 $n \le 100$ 的情况下消耗内存极少，符合 65536kB 的限制。



## M30912:累加树

构建 BST + 右-根-左累加 + BFS 输出, http://cs101.openjudge.cn/practice/30912

> 给定一个二叉搜索树先序遍历序列，将其转换为一棵累加树，并输出累加树的按层次遍历序列。
>
> 累加树和原二叉树形态相同，设原树上结点v在累加树上对应的结点是u，则u的值是原树上所有大于等于v的结点的和。
>
> 样例数据如下图所示，原树结点的值在圈内，对应累加树结点的值在圈外
>
> <img src="https://raw.githubusercontent.com/GMyhf/img/main/img/1749112066.png" alt="img" style="zoom:33%;" />
>
> **输入**
>
> 第1行：一个整数n,表示二叉搜索树有n个结点( 1 <= n <= 100)。
> 第2行，n个整数，本行表示二叉搜索树的先序遍历序列。每个整数范围是 [0, 10000]
>
> **输出**
>
> 对应的累加树的按层次遍历序列
>
> 样例输入
>
> ```
> 9
> 4 1 0 2 3 6 5 7 8
> ```
>
> 样例输出
>
> ```
> 30 36 21 36 35 26 15 33 8
> ```



解决该问题的思路可以分为三个主要步骤：

1. **重建二叉搜索树（BST）**：由于输入的是二叉搜索树的先序遍历序列，可以按照该序列的顺序依次将节点插入到树中，从而还原出原二叉树的结构。
2. **转换为累加树（GST）**：累加树的定义是每个节点的值等于原树中大于或等于该节点值的所有节点值之和。在二叉搜索树中，右子树的所有值都大于当前节点，左子树的所有值都小于当前节点。因此，可以通过**反向中序遍历**（右 -> 中 -> 左）的方式，从大到小遍历所有节点，并使用一个全局累加变量更新每个节点的值。
3. **层次遍历输出**：使用队列对转换后的累加树进行广度优先搜索（BFS），即可得到层次遍历序列。

Python 代码实现

```python
import sys
from collections import deque

# 定义二叉树节点
class TreeNode:
    def __init__(self, val=0):
        self.val = val
        self.left = None
        self.right = None

# 将节点插入到二叉搜索树中
def insert(root, val):
    if not root:
        return TreeNode(val)
    if val < root.val:
        root.left = insert(root.left, val)
    else:
        root.right = insert(root.right, val)
    return root

# 累加树转换辅助类
class GSTConverter:
    def __init__(self):
        self.running_sum = 0

    def convert(self, root):
        if not root:
            return
        # 反向中序遍历：先右子树，再当前节点，最后左子树
        self.convert(root.right)
        
        # 累加当前节点的值
        self.running_sum += root.val
        root.val = self.running_sum
        
        self.convert(root.left)

# 层次遍历二叉树
def level_order_traversal(root):
    if not root:
        return []
    result = []
    queue = deque([root])
    while queue:
        node = queue.popleft()
        result.append(node.val)
        if node.left:
            queue.append(node.left)
        if node.right:
            queue.append(node.right)
    return result

def main():
    # 读取标准输入
    input_data = sys.stdin.read().split()
    if not input_data:
        return
    
    n = int(input_data[0])
    preorder = [int(x) for x in input_data[1:n+1]]
    
    if n == 0:
        return

    # 1. 重建二叉搜索树
    root = None
    for val in preorder:
        root = insert(root, val)
    
    # 2. 转换为累加树
    converter = GSTConverter()
    converter.convert(root)
    
    # 3. 层次遍历
    ans = level_order_traversal(root)
    
    # 输出结果
    print(*(ans))

if __name__ == '__main__':
    main()
```

复杂度分析

- **时间复杂度**：
  - 重建 BST：在最坏情况下（树退化为链表），插入每个节点需要 $O(n)$ 时间，总时间为 $O(n^2)$。但由于本题 $n \le 100$，该方法在运行时间和空间上都是可行的。
  - 转换为累加树：反向中序遍历每个节点仅访问一次，时间复杂度为 $O(n)$。
  - 层次遍历：每个节点仅入队和出队一次，时间复杂度为 $O(n)$。
  - 总体时间复杂度在 $O(n^2)$ 以内，可轻松通过。

- **空间复杂度**：$O(n)$，用于存储二叉树的节点和层次遍历时的队列。



**思路**：根据二叉搜索树的先序遍历构建 BST（递归：第一个元素为根，剩余部分中小于根的在左子树，大于根的在右子树）。然后反向中序遍历（右-根-左）累加遍历过的节点值之和，更新每个节点的值为累加和。最后用 BFS 输出层序遍历。

```python
from collections import deque

def solve():
    data = list(map(int, sys.stdin.read().split()))
    n = data[0]
    pre = data[1:]

    def build(l, r):
        if l > r:
            return None
        root = pre[l]
        mid = r + 1
        for i in range(l + 1, r + 1):
            if pre[i] >= root:
                mid = i
                break
        return [root, build(l + 1, mid - 1), build(mid, r)]

    tree = build(0, n - 1)
    total = [0]

    def accum(node):
        if not node:
            return
        accum(node[2])
        total[0] += node[0]
        node[0] = total[0]
        accum(node[1])

    accum(tree)
    q = deque([tree])
    out = []
    while q:
        node = q.popleft()
        out.append(str(node[0]))
        if node[1]:
            q.append(node[1])
        if node[2]:
            q.append(node[2])
    print(" ".join(out))

if __name__ == "__main__":
    import sys
    solve()
```



