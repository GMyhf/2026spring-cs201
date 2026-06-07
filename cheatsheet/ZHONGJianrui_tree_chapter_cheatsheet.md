# 7 树

> 期末范围提示：PDF 明确列出树状数组、线段树、AVL、KD Tree、LCA 倍增为超纲；但 BIT/线段树可能作为多解工具保留。机考优先掌握二叉树遍历、BST、堆、哈夫曼。

## 7.2 树节点定义

一般 N 叉树节点：

```python
class Node:
    def __init__(self, val=None, children=None):
        self.val = val
        self.children = children if children is not None else []
```

长子-兄弟表示法：把一般多叉树转成二叉结构。

```python
class CSNode:
    def __init__(self, val):
        self.val = val
        self.first_child = None   # 第一个孩子
        self.next_sibling = None  # 右兄弟
```

二叉树节点，BST、AVL、按遍历序列建树时常用：

```python
class TreeNode:
    def __init__(self, val=0, left=None, right=None):
        self.val = val
        self.left = left
        self.right = right
```

按父子边表建一般树：

```python
def build_nary_tree(edges, root_val):
    # edges: [(parent, child), ...]
    nodes = {}

    def get(x):
        if x not in nodes:
            nodes[x] = Node(x)
        return nodes[x]

    for parent, child in edges:
        get(parent).children.append(get(child))

    return get(root_val)

def find_root(edges):
    # 根 = 出现过的所有节点 - 出现为孩子的节点
    all_nodes = set()
    child_nodes = set()

    for parent, child in edges:
        all_nodes.add(parent)
        all_nodes.add(child)
        child_nodes.add(child)

    return (all_nodes - child_nodes).pop()
```

## 7.3 树的遍历

递归版最常用，注意树题很多时候就是在遍历位置上做事。

```python
def preorder(root):
    # N 叉树前序：根 -> 各子树
    if not root:
        return []
    ans = [root.val]
    for child in root.children:
        ans.extend(preorder(child))
    return ans

def postorder(root):
    # N 叉树后序：各子树 -> 根
    if not root:
        return []
    ans = []
    for child in root.children:
        ans.extend(postorder(child))
    ans.append(root.val)
    return ans

def inorder(root):
    # 二叉树中序：左 -> 根 -> 右；一般 N 叉树没有唯一中序定义
    if not root:
        return []
    return inorder(root.left) + [root.val] + inorder(root.right)
```

二叉树迭代遍历：

```python
def preorder_iter(root):
    if not root:
        return []
    st = [root]
    ans = []
    while st:
        node = st.pop()
        ans.append(node.val)
        if node.right:
            st.append(node.right)
        if node.left:
            st.append(node.left)
    return ans

def inorder_iter(root):
    st = []
    ans = []
    cur = root
    while cur or st:
        while cur:
            st.append(cur)
            cur = cur.left
        cur = st.pop()
        ans.append(cur.val)
        cur = cur.right
    return ans

def postorder_iter(root):
    if not root:
        return []
    st = [(root, False)]
    ans = []
    while st:
        node, visited = st.pop()
        if visited:
            ans.append(node.val)
        else:
            st.append((node, True))
            if node.right:
                st.append((node.right, False))
            if node.left:
                st.append((node.left, False))
    return ans
```

Morris 遍历：二叉树的 `O(1)` 额外空间遍历方法。它会临时把“中序前驱”的右指针指回当前节点，第二次遇到时恢复指针；遍历结束后树结构不变。

```python
def morris_inorder(root):
    ans = []
    cur = root
    while cur:
        if not cur.left:
            ans.append(cur.val)
            cur = cur.right
        else:
            pre = cur.left
            while pre.right and pre.right is not cur:
                pre = pre.right
            if not pre.right:
                pre.right = cur      # 建临时线索，方便回到 cur
                cur = cur.left
            else:
                pre.right = None     # 第二次遇到，恢复指针
                ans.append(cur.val)
                cur = cur.right
    return ans
```

层序遍历：

```python
from collections import deque

def level_order(root):
    if not root:
        return []
    q = deque([root])
    ans = []

    while q:
        node = q.popleft()
        ans.append(node.val)
        for child in node.children:
            q.append(child)

    return ans
```

按层输出：

```python
from collections import deque

def level_by_level(root):
    if not root:
        return []
    q = deque([root])
    ans = []

    while q:
        level = []
        for _ in range(len(q)):
            node = q.popleft()
            level.append(node.val)
            for child in node.children:
                q.append(child)
        ans.append(level)

    return ans
```

## 7.4 树的递归常用模板

### 求高度

```python
def height(root):
    # 返回以 root 为根的树的节点高度；空树高度为 0
    if not root:
        return 0
    return max((height(child) for child in root.children), default=0) + 1
```

### 统计叶子节点

```python
def count_leaves(root):
    if not root:
        return 0
    if not root.children:
        return 1
    return sum(count_leaves(child) for child in root.children)
```

### 镜像树

```python
def mirror(root):
    if not root:
        return None
    root.children.reverse()
    for child in root.children:
        mirror(child)
    return root
```

### 判断平衡二叉树

```python
def is_balanced(root):
    # 返回-1表示不平衡，否则返回高度
    def dfs(node):
        if not node:
            return 0
        left = dfs(node.left)
        if left == -1:
            return -1
        right = dfs(node.right)
        if right == -1:
            return -1
        if abs(left - right) > 1:
            return -1
        return max(left, right) + 1

    return dfs(root) != -1
```

### 树上 DP

树上 DP 的核心是：先递归处理孩子子树，再把孩子状态合并到当前节点。大多数题是后序递归。

通用框架：

```python
def tree_dp(root):
    def dfs(node):
        if not node:
            return 空状态

        state = 初始状态(node)
        for child in node.children:
            child_state = dfs(child)
            state = 合并(state, child_state)

        return state

    return dfs(root)
```

子树大小：

```python
def subtree_size(root):
    size = {}

    def dfs(node):
        if not node:
            return 0
        total = 1
        for child in node.children:
            total += dfs(child)
        size[node] = total
        return total

    dfs(root)
    return size
```

树形选择 DP：相邻节点不能同时选，常见于“树上打家劫舍/最大权独立集”。

```python
def max_independent_set(root):
    # node.val 表示选中该节点的收益
    def dfs(node):
        if not node:
            return 0, 0  # (选 node, 不选 node)

        choose = node.val
        not_choose = 0

        for child in node.children:
            c_choose, c_not = dfs(child)
            choose += c_not                  # 选父节点，则孩子不能选
            not_choose += max(c_choose, c_not)

        return choose, not_choose

    choose, not_choose = dfs(root)
    return max(choose, not_choose)
```

树的直径：任意两点之间最长路径，N 叉树版本。

```python
def tree_diameter(root):
    ans = 0

    def dfs(node):
        # 返回从 node 往下走到叶子的最长边数
        nonlocal ans
        if not node:
            return 0

        best1 = best2 = 0
        for child in node.children:
            h = dfs(child) + 1
            if h > best1:
                best1, best2 = h, best1
            elif h > best2:
                best2 = h

        ans = max(ans, best1 + best2)  # 经过 node 的最长路径
        return best1

    dfs(root)
    return ans
```

换根 DP：先自底向上算子树答案，再自顶向下把父侧信息传给孩子。常见题：求每个点到所有点距离和。

```python
def sum_distances_in_tree(n, edges):
    graph = [[] for _ in range(n)]
    for u, v in edges:
        graph[u].append(v)
        graph[v].append(u)

    cnt = [1] * n   # 子树节点数
    ans = [0] * n   # ans[u] = u 到所有节点距离和

    def dfs1(u, parent):
        # 以 0 为根，先算 cnt[u] 和 ans[0] 的组成
        for v in graph[u]:
            if v == parent:
                continue
            dfs1(v, u)
            cnt[u] += cnt[v]
            ans[u] += ans[v] + cnt[v]

    def dfs2(u, parent):
        # 从 u 换根到 v：
        # v 子树内 cnt[v] 个点距离都 -1，其余 n-cnt[v] 个点距离都 +1
        for v in graph[u]:
            if v == parent:
                continue
            ans[v] = ans[u] - cnt[v] + (n - cnt[v])
            dfs2(v, u)

    dfs1(0, -1)
    dfs2(0, -1)
    return ans
```

## 7.4.1 LCA 最近公共祖先（倍增简版）

适用：多次询问树上两个点的最近公共祖先。预处理 `O(n log n)`，单次查询 `O(log n)`。`up[x][k]` 表示 `x` 向上跳 `2^k` 步到达的祖先。

```python
from collections import deque

def build_lca(n, graph, root=1):
    LOG = (n + 1).bit_length()
    up = [[0] * LOG for _ in range(n + 1)]
    depth = [-1] * (n + 1)
    depth[root] = 0
    q = deque([root])

    while q:
        u = q.popleft()
        for v in graph[u]:
            if depth[v] != -1:
                continue
            depth[v] = depth[u] + 1
            up[v][0] = u
            for k in range(1, LOG):
                up[v][k] = up[up[v][k - 1]][k - 1]
            q.append(v)

    def lift(x, d):
        for k in range(LOG):
            if (d >> k) & 1:
                x = up[x][k]
        return x

    def lca(a, b):
        if depth[a] < depth[b]:
            a, b = b, a
        a = lift(a, depth[a] - depth[b])
        if a == b:
            return a
        for k in range(LOG - 1, -1, -1):
            if up[a][k] != up[b][k]:
                a = up[a][k]
                b = up[b][k]
        return up[a][0]

    return lca, depth, up
```

## 7.5 二叉树根据遍历序列建树

前序 + 中序建树：

```python
def build_tree(preorder, inorder):
    # preorder: 根左右；inorder: 左根右
    pos = {x: i for i, x in enumerate(inorder)}
    idx = 0

    def dfs(l, r):
        nonlocal idx
        if l > r:
            return None

        root_val = preorder[idx]
        idx += 1
        root = TreeNode(root_val)

        mid = pos[root_val]
        root.left = dfs(l, mid - 1)
        root.right = dfs(mid + 1, r)
        return root

    return dfs(0, len(inorder) - 1)
```

后序 + 中序建树：

```python
def build_tree_from_post_in(postorder, inorder):
    # postorder: 左右根；从后往前取根
    pos = {x: i for i, x in enumerate(inorder)}
    idx = len(postorder) - 1

    def dfs(l, r):
        nonlocal idx
        if l > r:
            return None

        root_val = postorder[idx]
        idx -= 1
        root = TreeNode(root_val)

        mid = pos[root_val]
        # 注意：后序从后往前时要先建右子树
        root.right = dfs(mid + 1, r)
        root.left = dfs(l, mid - 1)
        return root

    return dfs(0, len(inorder) - 1)
```

中序 + 层序建树，默认节点值互异；中序负责划分左右子树，层序负责确定当前子树的根：

```python
def build_tree_from_in_level(inorder, levelorder):
    pos = {x: i for i, x in enumerate(inorder)}

    def dfs(l, r, level):
        if l > r or not level:
            return None

        root_val = level[0]      # 层序中第一个一定是当前子树根
        root = TreeNode(root_val)
        mid = pos[root_val]

        left_level = []
        right_level = []
        for x in level[1:]:
            # 按中序位置把剩余节点分到左右子树，同时保持层序相对顺序
            if pos[x] < mid:
                left_level.append(x)
            else:
                right_level.append(x)

        root.left = dfs(l, mid - 1, left_level)
        root.right = dfs(mid + 1, r, right_level)
        return root

    return dfs(0, len(inorder) - 1, levelorder)
```

前序 + 层序、后序 + 层序一般不能唯一确定二叉树。例如只有两个节点时，孩子在左边还是右边无法判断。若题目保证是满/真二叉树（这里模板只需要每个非叶节点都有两个孩子），则可以唯一建树。

满/真二叉树：前序 + 层序建树：

```python
def build_full_from_pre_level(preorder, levelorder):
    def dfs(pre, level):
        if not pre:
            return None
        root = TreeNode(pre[0])
        if len(pre) == 1:
            return root

        right_root = level[2]         # 满二叉树中 level[1], level[2] 是左右孩子根
        k = pre.index(right_root)     # 前序中右子树根的位置，用来切开左右子树

        left_pre = pre[1:k]
        right_pre = pre[k:]
        left_set = set(left_pre)

        left_level = [x for x in level if x in left_set]
        right_level = [x for x in level if x in set(right_pre)]

        root.left = dfs(left_pre, left_level)
        root.right = dfs(right_pre, right_level)
        return root

    return dfs(preorder, levelorder)
```

满/真二叉树：后序 + 层序建树：

```python
def build_full_from_post_level(postorder, levelorder):
    def dfs(post, level):
        if not post:
            return None
        root = TreeNode(post[-1])
        if len(post) == 1:
            return root

        left_root = level[1]          # 满二叉树中 level[1] 是左子树根
        k = post.index(left_root)     # 后序中左子树根的位置，用来切开左右子树

        left_post = post[:k + 1]
        right_post = post[k + 1:-1]
        left_set = set(left_post)

        left_level = [x for x in level if x in left_set]
        right_level = [x for x in level if x in set(right_post)]

        root.left = dfs(left_post, left_level)
        root.right = dfs(right_post, right_level)
        return root

    return dfs(postorder, levelorder)
```

## 7.6 序列化与反序列化

一般 N 叉树可以用“节点值 + 孩子数量”的前序格式，反序列化时按孩子数量递归读取。

```python
def serialize(root):
    data = []

    def dfs(node):
        if not node:
            return
        data.append(str(node.val))
        data.append(str(len(node.children)))  # 记录孩子数量，避免使用空节点标记
        for child in node.children:
            dfs(child)

    dfs(root)
    return data

def deserialize(data):
    # data 例如 ['A','2','B','0','C','1','D','0']
    if not data:
        return None
    it = iter(data)

    def dfs():
        val = next(it)
        cnt = int(next(it))
        node = Node(val)
        for _ in range(cnt):
            node.children.append(dfs())
        return node

    return dfs()
```

## 7.7 嵌套括号建树

常见格式类似：`A(B(C,D),E)`，可以用递归解析成一般树。不同题目格式可能不同，重点是“遇到括号递归处理所有孩子”。

```python
def parse_tree(s):
    # 示例格式：A(B(C,D),E)，逗号分隔孩子；默认节点值是单字符
    i = 0

    def dfs():
        nonlocal i
        if i >= len(s) or s[i] in ',)':
            return None

        node = Node(s[i])
        i += 1

        if i < len(s) and s[i] == '(':
            i += 1              # 跳过 '('
            while i < len(s) and s[i] != ')':
                child = dfs()    # 解析一个孩子子树
                if child:
                    node.children.append(child)
                if i < len(s) and s[i] == ',':
                    i += 1      # 跳过孩子之间的 ','
            i += 1              # 跳过 ')'

        return node

    return dfs()
```

## 7.8 二叉搜索树 BST

BST 性质：左子树所有值 `< root.val`，右子树所有值 `> root.val`。中序遍历有序。

应用场景：

- 动态维护有序集合：插入、查找、删除、前驱、后继。
- 需要利用“中序有序”性质：验证 BST、找第 `k` 小、范围内元素。
- 复杂度取决于树高 `h`，平均 `O(log n)`，最坏退化为 `O(n)`。

```python
def search_bst(root, x):
    while root:
        if x == root.val:
            return root
        elif x < root.val:
            root = root.left
        else:
            root = root.right
    return None

def insert_bst(root, x):
    if not root:
        return TreeNode(x)
    if x < root.val:
        root.left = insert_bst(root.left, x)
    elif x > root.val:
        root.right = insert_bst(root.right, x)
    return root

def min_node(root):
    while root and root.left:
        root = root.left
    return root

def max_node(root):
    while root and root.right:
        root = root.right
    return root
```

判断是否为 BST：

```python
def is_valid_bst(root):
    def dfs(node, low, high):
        if not node:
            return True
        if not (low < node.val < high):
            return False
        return dfs(node.left, low, node.val) and dfs(node.right, node.val, high)

    return dfs(root, float('-inf'), float('inf'))
```

## 7.9 AVL 树

AVL 是自平衡 BST，要求每个节点左右子树高度差不超过 1。插入或删除后如果失衡，用旋转恢复平衡。

应用场景：

- 需要动态有序集合，且希望查找、插入、删除都稳定为 `O(log n)`。
- 查询比普通 BST 更稳，不容易被有序输入退化。
- 如果题目只要求取最小/最大，不需要有序遍历，通常用堆更简单。

四种失衡类型：

- LL：新节点插入在左孩子的左侧，右旋。
- RR：新节点插入在右孩子的右侧，左旋。
- LR：新节点插入在左孩子的右侧，先左旋左孩子，再右旋当前根。
- RL：新节点插入在右孩子的左侧，先右旋右孩子，再左旋当前根。

```python
class AVLNode:
    def __init__(self, val):
        self.val = val
        self.left = None
        self.right = None
        self.height = 1

def h(node):
    return node.height if node else 0

def update(node):
    # 插入/旋转后刷新当前节点高度
    node.height = max(h(node.left), h(node.right)) + 1

def balance_factor(node):
    # > 1 表示左重，< -1 表示右重
    return h(node.left) - h(node.right)

def right_rotate(y):
    # LL 或 LR 的最后一步：y 下沉到右侧
    x = y.left
    t = x.right
    x.right = y
    y.left = t
    update(y)
    update(x)
    return x

def left_rotate(x):
    # RR 或 RL 的最后一步：x 下沉到左侧
    y = x.right
    t = y.left
    y.left = x
    x.right = t
    update(x)
    update(y)
    return y

def insert_avl(root, val):
    # 先按普通 BST 插入
    if not root:
        return AVLNode(val)
    if val < root.val:
        root.left = insert_avl(root.left, val)
    elif val > root.val:
        root.right = insert_avl(root.right, val)
    else:
        return root

    update(root)
    bf = balance_factor(root)

    # LL
    if bf > 1 and val < root.left.val:
        return right_rotate(root)
    # RR
    if bf < -1 and val > root.right.val:
        return left_rotate(root)
    # LR
    if bf > 1 and val > root.left.val:
        root.left = left_rotate(root.left)
        return right_rotate(root)
    # RL
    if bf < -1 and val < root.right.val:
        root.right = right_rotate(root.right)
        return left_rotate(root)

    return root
```

## 7.10 堆与优先队列

Python 的 `heapq` 是小根堆。

```python
import heapq

h = []
heapq.heappush(h, x)
smallest = heapq.heappop(h)

# 列表原地建堆，O(n)
a = [5, 1, 3]
heapq.heapify(a)
```

完全二叉堆的数组下标：

```text
parent = (i - 1) // 2
left = 2 * i + 1
right = 2 * i + 2
heapify 建堆复杂度 O(n)，逐个 heappush 建堆 O(n log n)
```

手写小根堆上浮/下沉：

```python
def sift_up(h, i):
    while i > 0:
        p = (i - 1) // 2
        if h[p] <= h[i]:
            break
        h[p], h[i] = h[i], h[p]
        i = p

def sift_down(h, i):
    n = len(h)
    while True:
        l = 2 * i + 1
        r = 2 * i + 2
        smallest = i
        if l < n and h[l] < h[smallest]:
            smallest = l
        if r < n and h[r] < h[smallest]:
            smallest = r
        if smallest == i:
            break
        h[i], h[smallest] = h[smallest], h[i]
        i = smallest

def build_heap(h):
    for i in range(len(h) // 2 - 1, -1, -1):
        sift_down(h, i)
```

大根堆用负数：

```python
heapq.heappush(h, -x)
largest = -heapq.heappop(h)
```

自定义对象入堆时，要定义 `__lt__`：

```python
class Item:
    def __init__(self, weight, char):
        self.weight = weight
        self.char = char

    def __lt__(self, other):
        # heapq 会用 < 比较对象；这里先按权重，再按字符打破平局
        if self.weight == other.weight:
            return self.char < other.char
        return self.weight < other.weight
```

应用场景：

- 动态反复取当前最小/最大值。
- Dijkstra
- Top K
- 合并若干有序序列
- 哈夫曼编码
- 优先级队列模拟

## 7.11 哈夫曼树

哈夫曼编码是贪心算法：每次取权值最小的两棵树合并。

应用场景：

- 最优前缀编码：高频字符短编码，低频字符长编码。
- 最小合并代价：每次合并两个最小权值，例如“合并果子/石子”。
- 计算最小带权路径长度 `WPL`。

计算带权路径长度 WPL：

```python
import heapq

def huffman_wpl(weights):
    # weights 是所有叶子权重
    h = weights[:]
    heapq.heapify(h)
    ans = 0

    while len(h) > 1:
        a = heapq.heappop(h)
        b = heapq.heappop(h)
        s = a + b
        ans += s          # 两个节点深度都加1，对WPL贡献为a+b
        heapq.heappush(h, s)

    return ans
```

```python
import heapq
from itertools import count

class HuffmanNode:
    def __init__(self, weight, char=None, left=None, right=None):
        self.weight = weight
        self.char = char      # 叶子节点存字符；内部节点为 None
        self.left = left
        self.right = right

def build_huffman_tree(freq):
    if not freq:
        return None

    counter = count()
    heap = []

    for ch, w in freq.items():
        node = HuffmanNode(w, ch)
        heapq.heappush(heap, (w, next(counter), node))

    while len(heap) > 1:
        w1, _, left = heapq.heappop(heap)
        w2, _, right = heapq.heappop(heap)

        parent = HuffmanNode(w1 + w2, None, left, right)
        heapq.heappush(heap, (parent.weight, next(counter), parent))

    return heap[0][2]

def get_codes(root):
    codes = {}

    def dfs(node, path):
        if not node:
            return

        # 叶子节点
        if node.char is not None:
            codes[node.char] = path or "0"   # 只有一个字符时编码为 0
            return

        dfs(node.left, path + "0")
        dfs(node.right, path + "1")

    dfs(root, "")
    return codes
```

哈夫曼编码特点：

- 高频字符编码短，低频字符编码长。
- 编码是前缀码，任一字符编码都不是另一个字符编码的前缀。
- 构造过程依赖小根堆。

## 7.12 树状数组 BIT / Fenwick Tree

树状数组适合维护前缀和：单点修改 `O(log n)`，查询前缀和/区间和 `O(log n)`。核心是 `lowbit(x) = x & -x`。

应用场景：

- 动态数组的单点修改 + 前缀和/区间和查询。
- 逆序对、排名统计、前缀计数，常配合离散化。
- 只维护一维区间和时，比线段树更短；若要维护复杂区间信息，优先考虑线段树。

```python
class BIT:
    def __init__(self, n):
        self.n = n
        self.bit = [0] * (n + 1)   # 内部使用 1-index

    def add(self, i, delta):
        # 外部传入 0-index，把 nums[i] 增加 delta
        i += 1
        while i <= self.n:
            self.bit[i] += delta
            i += i & -i            # 跳到下一个覆盖 i 的节点

    def prefix(self, i):
        # 查询 nums[0] + ... + nums[i]
        i += 1
        ans = 0
        while i > 0:
            ans += self.bit[i]
            i -= i & -i            # 跳到上一个前缀块
        return ans

    def query(self, l, r):
        # 查询闭区间 [l, r]
        if l > r:
            return 0
        return self.prefix(r) - (self.prefix(l - 1) if l > 0 else 0)
```

## 7.13 线段树

适用于动态维护区间信息，例如区间和、区间最大值、区间最小值。下面是非递归区间和版本。

应用场景：

- 动态区间查询：区间和、区间最大值、区间最小值。
- 区间修改 + 区间查询：通常需要懒标记。
- 信息可合并的问题：父区间答案由左右子区间答案合并得到。
- 比树状数组更通用，但代码更长。

```python
class SegmentTree:
    def __init__(self, nums):
        self.n = len(nums)
        self.tree = [0] * (2 * self.n)

        # 叶子节点
        for i, x in enumerate(nums):
            self.tree[self.n + i] = x

        # 自底向上建树
        for i in range(self.n - 1, 0, -1):
            self.tree[i] = self.tree[2 * i] + self.tree[2 * i + 1]

    def update(self, i, val):
        # 单点修改 nums[i] = val
        i += self.n
        self.tree[i] = val
        i //= 2

        while i:
            self.tree[i] = self.tree[2 * i] + self.tree[2 * i + 1]
            i //= 2

    def query(self, l, r):
        # 查询闭区间 [l, r] 的和
        l += self.n
        r += self.n
        ans = 0

        while l <= r:
            if l % 2 == 1:
                ans += self.tree[l]
                l += 1
            if r % 2 == 0:
                ans += self.tree[r]
                r -= 1
            l //= 2
            r //= 2

        return ans
```

如果题目或模板习惯半开区间 `[l, r)`，查询部分常写成：

```python
def query_half_open(tree, n, l, r):
    # tree 是长度 2*n 的线段树数组，查询 [l, r)
    l += n
    r += n
    ans = 0

    while l < r:
        if l % 2 == 1:
            ans += tree[l]
            l += 1
        if r % 2 == 1:
            r -= 1
            ans += tree[r]
        l //= 2
        r //= 2

    return ans
```

对比：

- 前缀和：静态区间和，查询 `O(1)`，修改 `O(n)`。
- 树状数组：单点修改 + 前缀/区间和，`O(log n)`，代码短。
- 线段树：更通用，可维护最大值、最小值、区间和、懒标记等。

## 7.14 Trie 字典树

适用于字符串集合的前缀查询。

应用场景：

- 判断字符串是否存在、统计某个前缀出现次数。
- 前缀匹配、自动补全、字典序搜索。
- 二进制 Trie：把数字按位插入，用于最大异或对。

```python
class Trie:
    def __init__(self):
        self.children = {}
        self.end = False

    def insert(self, word):
        node = self
        for ch in word:
            if ch not in node.children:
                node.children[ch] = Trie()
            node = node.children[ch]
        node.end = True

    def search(self, word):
        node = self
        for ch in word:
            if ch not in node.children:
                return False
            node = node.children[ch]
        return node.end

    def startswith(self, prefix):
        node = self
        for ch in prefix:
            if ch not in node.children:
                return False
            node = node.children[ch]
        return True
```

常见应用：

- 判断字符串是否存在
- 前缀匹配
- 自动补全
- 字典序搜索
- 最大异或对（把二进制位当作字符）

## 7.15 树题常见思路

- 遇到“从根到叶”：DFS + path。
- 遇到“按层”：BFS + deque。
- 遇到“子树信息”：后序递归，先算所有孩子子树再合并。
- 遇到“BST”：二叉树专用，利用中序有序或上下界限制。
- 遇到“最近/最小/最大”：可能用堆或 BST。
- 遇到“字符串集合前缀”：Trie。
- 遇到“动态区间”：树状数组或线段树。
- 遇到“构建树”：一般树常由边表/父数组建；二叉树常由遍历序列建。

树的递归万能框架：

```python
def dfs(node):
    if not node:
        return 空值

    # 前序位置：刚进入节点，适合记录路径、访问根
    child_results = []
    for child in node.children:
        child_results.append(dfs(child))
    # 后序位置：已经知道所有孩子子树信息，适合求高度、子树大小、直径等

    return 合并(child_results)
```
