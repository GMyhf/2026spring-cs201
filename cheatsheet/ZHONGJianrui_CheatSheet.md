## **==认真读题，注意输入输出格式==**
## **==留意变量范围，考虑算法的时间复杂度是否够用==**

## 复杂度速查
| 数据规模 | 常见可接受复杂度 |
|---|---|
| n <= 20 | 状压/回溯 `O(2^n)` |
| n <= 500 | `O(n^3)` 可试 |
| n <= 5000 | `O(n^2)` 看时限 |
| n <= 1e5 | `O(n log n)` / `O(n)` |
| n >= 1e6 | 尽量 `O(n)`，注意 I/O |
## Pycharm 调试快捷键
单步跳过：F8
步入函数：F7
跳出当前函数：Shift + F8
运行到光标处：Alt + F9
继续执行：F9
# 0 少量语法
## 0.1 输入
不定行输入的几种读法：
```python
#try...except捕获异常
while True:
    try:
        #正常读取
    except EOFError:
        break
#利用sys逐行读取
import sys
for line in sys.stdin:
    line = line.strip()
    #处理每一行
#同样适用于一般的输入读取特别是大量数据输入流优化
data = sys.stdin.read()
lines = data.splitlines()
for line in lines:
    #处理每一行
#或者
data = sys.stdin.read().split()
idx = 0
#然后读取data[idx]
#最简单的方法
import sys
input = sys.stdin.buffer.readline
```
## 0.2 输出
f-string 输出格式
```python
#保留两位小数
print(f'{ans:.2f}')
#左对齐、右对齐和居中
print(f"{10:5}")      # 右对齐
print(f"{10:<5}")     # 左对齐
print(f"{10:^5}")     # 居中
#补零
print(f'{ans:03}')    #例如ans = 7, 输出007
```
## 0.3 可变与不可变对象
常用不可变对象：整数，浮点数，==字符串==，==元组== 等。
常用可变对象：列表，字典，集合。
==重要：==可变对象作为参数传入函数，若在函数内被修改，会影响外部变量。
### 0.3.1 深拷贝和浅拷贝
深拷贝：
```python
import copy
b = copy.deepcopy(a)
```
浅拷贝：
```python
b = a.copy()
```
# 1 一些基础的知识
## 1.1 string有关函数
```python
len(s)             # 返回长度
s[0]
s[1:4],s[:2],s[3:] # 注意和range一样不包含右端点
s[::-1]            # 反转字符，完整用法s[start:end:step]
print(s[::-1])     # feDcBa
s.lower()          # 转小写
s.upper()          # 转大写
s.swapcase()       # 大小写互换
s.capitalize()     # 字符串的首字母大写
s.title()          # 每个单词首字母大写
s.strip()          # 去除首尾空白字符，可以指定，例如s.strip(',')
s.find(s1)         # 查找第一次出现s1的位置，没找到返回-1
s.startswith(s1)   # 判断是否为前缀
s.endswith(s1)     # 判断是否为后缀
s.replace('a','b') # 替换，注意字符串不可变。故返回一个新字符串
s.split()          # 可以指定字符
''.join(lst)       # 可以指定字符
s.count(s1)        # 统计某个字符串的出现次数
s1 in s            # 判断是否为子串
from collections import Counter
cnt = Counter(s)   # 统计各字符出现频率，cnt为字典
```
## 1.2 数据类型转换
```python
int(s,2)           # 指定进制，并注意int是截断不能向下取整！例如int(-3.14) = -3
str(),list(),set()
ord(s)             # 返回字符s的ASCII码
chr(n)             # 返回ASCII码为n的字符
```
## 1.3 dict有关函数
```python
d.keys(),d.values(),d.items()
d1.update(d2)                           # 合并并更新
d.clear()                               # 清除
sorted(d)                               # 按键排序
sorted(d.items(), key = lambda x: x[1]) # 按值排序
from collections import defaultdict     # 好用爱用
defaultdict(lambda : -1)
#注意defaultdict会自动创建默认值，一般字典会报错
```
## 1.4 set有关函数
集合常用于==查重、去重==和==快速判断元素是否存在==，平均复杂度为 $O(1)$。其他情况不如list
```python
s = set()            # 空集合，注意{}是空字典
s = set(lst)         # 列表去重
lst = list(s)
x in s               # 判断元素是否存在
len(s)
s.add(x)             # 添加元素
s.remove(x)          # 删除元素，不存在会报错
s.discard(x)         # 删除元素，不存在不报错
s.clear()            # 清空集合
# 集合运算
a | b                # 并集
a & b                # 交集
a - b                # 差集：在a中不在b中
a ^ b                # 对称差集：只在其中一个集合中
a.union(b)
a.intersection(b)
a.difference(b)
a.symmetric_difference(b)
a <= b               # a是否为b的子集
a >= b               # a是否为b的超集
a.isdisjoint(b)      # 是否没有交集
```
## 1.5 math库
```python
math.ceil(),math.floor()
math.isqrt(x)            # 整数平方根，返回不超过sqrt(x)的最大整数
math.factorial()
math.gcd(a, b), math.lcm(a, b)
math.log(x,base)
```
## 1.6 自定义排序
```python
from functools import cmp_to_key
def my_cmp(a, b):
    if f(a) < f(b):
        return -1
    if f(a) > f(b):
        return 1
    return 0
lst.sort(key = cmp_to_key(my_cmp))
```
### 1.6.1 排序速查
```python
a.sort()                         # 原地排序
b = sorted(a)                    # 返回新列表
a.sort(reverse=True)
a.sort(key=lambda x: (x[0], -x[1]))  # 多关键字：先升序x[0]，再降序x[1]
```
排序性质：
| 算法 | 平均复杂度 | 稳定性 | 常见用途 |
|---|---|---|---|
| 快排 | `O(n log n)` | 不稳定 | 原地分治，注意 pivot |
| 归并 | `O(n log n)` | 稳定 | 逆序对、跨区间统计 |
| 堆排 | `O(n log n)` | 不稳定 | Top K、优先级队列 |
| 插入 | `O(n^2)` | 稳定 | 小规模/近乎有序 |
## 1.7 位运算
在处理二进制相关运算时速度更快
| 运算符  | 说明   | 示例     | 结果（二进制）                 |
| ---- | ---- | ------ | ----------------------- |
| `&`  | 按位与  | 5 & 3  | 0101 & 0011 = 0001 → 1  |
| `\|` | 按位或  | 5 \| 3 | 0101 \| 0011 = 0111 → 7 |
| `^`  | 按位异或 | 5 ^ 3  | 0101 ^ 0011 = 0110 → 6  |
| `~`  | 按位取反 | ~5     | ~0101 = -6（补码表示）        |
| `<<` | 左移   | 5 << 1 | 0101 << 1 = 1010 → 10   |
| `>>` | 右移   | 5 >> 1 | 0101 >> 1 = 0010 → 2    |
### 位运算常用技巧
```python
x.bit_count()              # 二进制中1的个数
x.bit_length()             # 二进制有效位长度
lowbit = x & -x            # 取最低位的1
x & (x-1) == 0             # 判断x是否为2的幂，x>0时成立
mask = (1 << k) - 1        # k位全1掩码
# 枚举mask的非空子集
sub = mask
while sub:
    # 处理sub
    sub = (sub - 1) & mask
# 固定k位二进制滑动窗口
num = ((num << 1) | bit) & mask
```
## 1.8 enumerate()
```python
enumerate(iterable, start = ) # 默认为从零开始
# 应用举例
lst = list(enumerate(map(int,input().split())))
for i, x in enumerate(s):
```

# 2 常用技术和模板
## 2.1 素数筛法
```python
# 经典款，复杂度O(nlog(logn))
def sieve(n):
    is_prime = [True] * (n+1)
    is_prime[0] = is_prime[1] = False
    for i in range(2, int(n**0.5)+1):
        if is_prime[i]:
            for j in range(i*i, n+1, i):
                is_prime[j] = False
    return is_prime
#欧拉筛，复杂度O(n)
def linear_sieve(n):
    is_prime = [True] * (n+1)
    primes = []
    is_prime[0] = is_prime[1] = False
    for i in range(2, n+1):
        if is_prime[i]:
            primes.append(i)
        for p in primes:
            if i*p > n:
                break
            is_prime[i*p] = False
            if i%p == 0:
                break
    return primes
```
## 2.2 二分查找
带有标签binary search的题目，使用方法类似于取逆后优化枚举。
```python
# 查找左边界（第一个不小于target的位置）
def lower_bound(arr, target):
    l, r = 0, len(arr)
    while l < r:
        mid = (l+r)//2
        if arr[mid] < target:
            l = mid+1
        else:
            r = mid
    return l
# 查找右边界（最后一个不超过target的位置）
def last_le(arr, target):
    l, r = 0, len(arr)-1
    ans = -1
    while l <= r:
        mid = (l+r)//2
        if arr[mid] <= target:
            ans = mid
            l = mid+1
        else:
            r = mid-1
    return ans
# 内置函数
import bisect
bisect.bisect_left(a, x)  # 第一个不小于x的下标
bisect.bisect_right(a, x) # 第一个大于x的下标
bisect.insort_left(a, x)  # 在左侧插入x
bisect.insort_right(a, x) # 在右侧插入x，与上面的区别在循环时可能体现出来
```
==注意==：如果数组降序排列取一个负号即可
### 2.2.1 二分答案
当答案满足“越大越可行/越小越可行”的单调性时，把判定函数 `check(x)` 写出来，再二分答案。
```python
# 找最小可行值：check(x) 为 True 表示 x 已经够大/够好
def binary_answer(l, r):
    while l < r:
        mid = (l + r) // 2
        if check(mid):
            r = mid
        else:
            l = mid + 1
    return l

# 找最大可行值：check(x) 为 True 表示 x 仍然可行
def binary_answer_max(l, r):
    while l < r:
        mid = (l + r + 1) // 2
        if check(mid):
            l = mid
        else:
            r = mid - 1
    return l
```
## 2.3 康托展开
找排列按字典序的顺次
```python
import math
# 从排列到康托展开
def cantor_expansion(P):
    n = len(P)
    nums = sorted(P)
    ans = 0
    for i in range(n):
        index = nums.index(P[i])
        ans += index*math.factorial(n-i-1)
        nums.pop(index)
    return ans
# 从康托展开到排列
def inverse_cantor(N, n):
    import math
    nums = list(range(1, n+1))
    P = []
    for i in range(n,0,-1):
        f = math.factorial(i-1)
        index = N//f
        P.append(nums.pop(index))
        N %= f
    return P
```
## 2.4 Kadane 算法
求连续子数组的最大和
```python
def kadane(arr):
    max_ending_here = max_so_far = arr[0]
    for x in arr[1:]:
        max_ending_here = max(x, max_ending_here+x)
        max_so_far = max(max_so_far, max_ending_here)
    return max_so_far
```
## 2.4.1 贪心速查
- 先排序再局部选择：区间调度、最小/最大资源数、配对问题。
- 维护当前最优：能证明“局部最优不会影响后续更优解”再贪心。
- 常见关键词：最少次数、最早结束、最大收益、交换相邻元素后不变差。
- 不确定时先找反例：小规模枚举/手推能否推翻局部选择。
## 2.4.2 前缀和
前缀和适合“频繁查询区间和/子数组和”，也常和哈希表、取模、滑动窗口配合。
```python
# 一维前缀和：sum(l..r) = pre[r+1] - pre[l]
pre = [0]
for x in a:
    pre.append(pre[-1] + x)
s = pre[r + 1] - pre[l]

# 统计和为 target 的子数组个数
from collections import defaultdict
cnt = defaultdict(int)
cnt[0] = 1
cur = ans = 0
for x in a:
    cur += x
    ans += cnt[cur - target]
    cnt[cur] += 1

# 二维前缀和：矩形 [x1..x2][y1..y2]
pre = [[0] * (m + 1) for _ in range(n + 1)]
for i in range(n):
    for j in range(m):
        pre[i+1][j+1] = pre[i][j+1] + pre[i+1][j] - pre[i][j] + grid[i][j]
rect = pre[x2+1][y2+1] - pre[x1][y2+1] - pre[x2+1][y1] + pre[x1][y1]
```
## 2.5 分治算法
简单版本的应用：计算方幂时间复杂度为O(log n)
举例：M29741 神经网络之国
```python
n,l,m = map(int,input().split())
cost_in = list(map(int,input().split()))
cost = list(map(int,input().split()))
cost_out = list(map(int,input().split()))
cm = [0]*m
for i in range(n):
    cm[cost[i]%m] += 1
Mod = 10**9+7
dp = [0]*m
for i in range(n):
    dp[cost_in[i]%m] += 1
def cal(x,f):
    if x == 1:
        return f
    elif not x%2:
        d = cal(x//2,f)
        return [sum(d[j]*d[(k-j)%m] for j in range(m))%Mod for k in range(m)]
    else:
        d = cal(x-1,f)
        return [sum(f[j]*d[(k-j)%m] for j in range(m))%Mod for k in range(m)]
if l > 2:
    cnt = cal(l-2,cm)
else:
    cnt = [0]*m
    cnt[0] = 1
dp = [sum(dp[k]*cnt[j-k] for k in range(m)) for j in range(m)]
ans = sum(dp[(-cost[j]-cost_out[j])%m] for j in range(n))%Mod
print(ans)
```
## 2.6 归并排序（统计逆序对）
也可以用于统计其他满足条件的跨区间数对
```python
def count(arr):
    def merge(l,r):
        if l >= r:
            return 0
        mid = (l+r)//2
        ans = merge(l,mid)
        ans += merge(mid+1,r)
        ans += _merge(l,mid,r)
        return ans
    def _merge(l,mid,r):
        temp = arr[l:r+1]
        i,j = 0,mid+1-l
        k = l
        ans = 0
        while i <= mid-l and j <= r-l:
            if temp[i] <= temp[j]:
                arr[k] = temp[i]
                i += 1
            else:
                arr[k] = temp[j]
                ans += mid-l+1-i
                j += 1
            k += 1
        while i <= mid-l:
            arr[k] = temp[i]
            k += 1
            i += 1
        while j <= r-l:
            arr[k] = temp[j]
            k += 1
            j += 1
        return ans
    #如果条件变化，先统计跨区间贡献再正常归并。
    def _merge(l, mid, r):
	    ans = 0
	    j = mid + 1
	    for i in range(l, mid + 1):
	        while j <= r and arr[i] > arr[j]:
	            j += 1
	        ans += j - (mid + 1)
	    temp = []
	    i, j = l, mid + 1
	    while i <= mid and j <= r:
	        if arr[i] <= arr[j]:
	            temp.append(arr[i])
	            i += 1
	        else:
	            temp.append(arr[j])
	            j += 1
	    temp.extend(arr[i:mid + 1])
	    temp.extend(arr[j:r + 1])
	    arr[l:r + 1] = temp
	    return ans
    return merge(0, len(arr)-1)
```
## 2.7 KMP
求模式串 `p` 在文本串 `s` 中所有出现位置，核心是 `lps`：当前匹配失败时跳到最长相等真前后缀。
```python
def get_lps(p):
    lps = [0] * len(p)
    j = 0
    for i in range(1, len(p)):
        while j > 0 and p[i] != p[j]:
            j = lps[j - 1]       # 失配，跳到上一个可复用前缀
        if p[i] == p[j]:
            j += 1
        lps[i] = j
    return lps

def kmp(s, p):
    if not p:
        return list(range(len(s) + 1))
    lps = get_lps(p)
    ans = []
    j = 0                        # 当前已匹配的模式串长度
    for i, ch in enumerate(s):
        while j > 0 and ch != p[j]:
            j = lps[j - 1]
        if ch == p[j]:
            j += 1
        if j == len(p):
            ans.append(i - len(p) + 1)
            j = lps[j - 1]       # 继续找下一个匹配，可处理重叠
    return ans

# 最小循环节：如 ababab 的循环节为 ab
def min_period(s):
    lps = get_lps(s)
    n = len(s)
    p = n - lps[-1]
    return p if n % p == 0 else n
```
## 2.8 Manacher
线性求最长回文子串长度。先插入 `#` 统一奇偶回文，`p[i]` 表示以 `i` 为中心的回文半径。
```python
def manacher(s):
    t = '^#' + '#'.join(s) + '#$'
    p = [0] * len(t)
    center = right = 0
    ans = 0
    for i in range(1, len(t) - 1):
        mirror = 2 * center - i
        if i < right:
            p[i] = min(right - i, p[mirror])  # 利用已知回文范围
        while t[i + p[i] + 1] == t[i - p[i] - 1]:
            p[i] += 1
        if i + p[i] > right:
            center = i
            right = i + p[i]
        ans = max(ans, p[i])
    return ans

# 简单题可用中心扩展：O(n^2)，更好写
def longest_pal_center(s):
    ans = 0
    for c in range(len(s)):
        for l, r in ((c, c), (c, c + 1)):
            while l >= 0 and r < len(s) and s[l] == s[r]:
                ans = max(ans, r - l + 1)
                l -= 1
                r += 1
    return ans
```
# 3 一些数据结构
## 3.0 倒排索引
即一般搜索引擎的基础算法
```python
# files[i] 表示第i个词出现在哪些文档中
files = [set(...), set(...), ...]
must = [i for i, x in enumerate(query) if x == 1]
ban = [i for i, x in enumerate(query) if x == -1]
res = set(files[must[0]])
for i in must[1:]:
    res &= files[i]
bad = set()
for i in ban:
    bad |= files[i]
res -= bad
print(*sorted(res)) if res else print("NOT FOUND")
```
## 3.1 并查集
优化：路径压缩，按秩合并（关注树高），按大小合并（关注树的节点数，例如可以统计连通块节点数）
```python
class DSU:
    def __init__(self, n):
        self.parent = list(range(n))
        self.size = [1] * n  # 可选
    def find(self, x):
        if self.parent[x] != x:
            self.parent[x] = self.find(self.parent[x])
        return self.parent[x]
    def union(self, x, y): # 按大小合并
        rx = self.find(x)
        ry = self.find(y)
        if rx == ry:
            return False
        if self.size[rx] < self.size[ry]:
            rx, ry = ry, rx
        self.parent[ry] = rx
        self.size[rx] += self.size[ry]
        return True
#带members的
class DSUWithMembers:
    def __init__(self, n):
        self.parent = list(range(n))
        self.size = [1] * n
        self.members = [[i] for i in range(n)]  # 只有根节点的members有效
    def find(self, x):
        # 路径压缩，只改变parent，不改变members
        if self.parent[x] != x:
            self.parent[x] = self.find(self.parent[x])
        return self.parent[x]
    def union(self, x, y):
        rx, ry = self.find(x), self.find(y)
        if rx == ry:
            return False
        # 小集合并入大集合，减少members合并总成本
        if self.size[rx] < self.size[ry]:
            rx, ry = ry, rx
        self.parent[ry] = rx
        self.size[rx] += self.size[ry]
        self.members[rx].extend(self.members[ry])    #注意两者元素不重，用extend更快
        self.members[ry] = []       # 非根节点的members不再使用
        return True
```
## 3.2 双指针
### 3.2.0 快慢指针
常用于链表/序列判环、找中点，也可以处理“速度不同导致相遇”的模拟题。
```python
# 链表找中点：slow 到中点，fast 每次走两步
slow = fast = head
while fast and fast.next:
    slow = slow.next
    fast = fast.next.next

# Floyd 判环：若有环，slow 和 fast 一定会相遇
def has_cycle(head):
    slow = fast = head
    while fast and fast.next:
        slow = slow.next
        fast = fast.next.next
        if slow is fast:
            return True
    return False
```
###  3.2.1 一种双指针的简单用法
双指针可以减少很多无效的暴力枚举。有时可以用for循环来起到快指针的作用，例如（题目为Leetcode 283. 移动零）：
```python
class Solution:
    def moveZeroes(self, nums: List[int]) -> None:
        size = len(nums)
        p1 = 0
        for p2 in range(size):
            if nums[p2] != 0:
                nums[p1], nums[p2] = nums[p2], nums[p1]
                p1+=1
```
### 3.2.2 滑动窗口
一个较为清晰的写法如下（Leetcode 209. 长度最小的子数组）：
```python
class Solution:
    def minSubArrayLen(self, target: int, nums: List[int]) -> int:
        s = 0
        size = len(nums)
        minlen = size + 1
        left = 0
        for right in range(size): # 每次将右边界扩展一位
            s += nums[right]
            #尽可能缩小窗口
            while s >= target:
                minlen = min(minlen, right - left + 1)
                s-=nums[left]
                left+=1
        return minlen if minlen < size + 1 else 0
```
## 3.3 栈
例：谢正茂老师班计概2025秋期末机考Q6T: 化学分子式
（注意：为什么往栈中添加元素的时候先添加分子式前的字符？因为原子/原子团的个数在后面，我们处理到各个元素符号时是不知道个数的，只有得到后面的“个数”这个信息才能进行计算。所以从前往后入栈，遇到数字就能够把之前暂存的一些栈顶元素给清掉）
```python
def extract(ls): # 提取元素名称
    if len(ls) >= 2 and ls[-2].islower():
        return "".join(ls[-2:][::-1])
    else:
        return ls[-1]
def encrypt(ls): # 这个ls是反转的分子式字符列表（这么处理只是为了写起来方便）
    stack = []
    while ls:
        if ls[-1] == '(':
            stack.append(ls.pop())
        elif ls[-1] == ')':
            ls.pop()
            helpsum = 0
            while stack[-1] != '(':
                helpsum += stack.pop()
            stack.pop()
            stack.append(helpsum)
        elif not ls[-1].isdigit():
            ele = extract(ls)
            if len(ele) == 2:
                ls.pop()
                ls.pop()
            else:
                ls.pop()
            stack.append(mydict[ele])
        else:
            helpstack = []
            while ls and ls[-1].isdigit():
                helpstack.append(ls.pop())
            coef = int("".join(helpstack))
            stack[-1] *= coef
    return sum(stack)
m, n = map(int, input().split())
mydict = {}
for _ in range(m):
    element, mass = input().split()
    mass = int(mass)
    mydict[element] = mass
for _ in range(n):
    string = input().strip()
    ls = [c for c in string]
    print(encrypt(ls[::-1]))
```
### 3.3.1 单调栈
==寻找最近的更大/更小元，或者计算区间贡献==
对于与栈中元素原位置有关的问题，栈存下标一般是好的选择。例如Leetcode 84. 柱状图中最大的矩形：
```python
class Solution:
    def largestRectangleArea(self, heights: List[int]) -> int:
        n = len(heights)
        stack = []
        resultr = [n]*n
        for i in range(n):
            while stack and heights[i] < heights[stack[-1]]:
                index = stack.pop()
                resultr[index] = i
            stack.append(i)
        resultl = [-1]*n
        stack = []
        for i in range(n-1,-1,-1):
            while stack and heights[i] < heights[stack[-1]]:
                index = stack.pop()
                resultl[index] = i
            stack.append(i)
        return max(heights[i]*(resultr[i]-resultl[i]-1) for i in range(n))
```
### 3.3.2 辅助栈
例：快速堆猪
用辅助栈：用一个单调栈维护最小值，再用另外一个栈维护其余的值。
```python
stack = []
ans = []
while True:
    try:
        s = input()
        if s == 'pop':
            if stack:
                w = stack.pop()
                if ans and w == ans[-1]:
                    ans.pop()
        elif s == 'min':
            if ans:
                print(ans[-1])
        else:
            t = list(s.split())
            k = int(t[-1])
            if not ans or k <= ans[-1]:
                ans.append(k)
            stack.append(k)
    except EOFError:
        break
```
### 3.3.3 懒删除
还是快速堆猪
```python
import heapq
from collections import defaultdict
out = defaultdict(int)
pigs_heap = []
pigs_stack = []
while True:
    try:
        s = input()
    except EOFError:
        break
    if s == "pop":
        if pigs_stack:
            out[pigs_stack.pop()] += 1
    elif s == "min":
        if pigs_stack:
            while True:
                x = heapq.heappop(pigs_heap)
                if not out[x]:
                    heapq.heappush(pigs_heap, x)
                    print(x)
                    break
                out[x] -= 1
    else:
        y = int(s.split()[1])
        pigs_stack.append(y)
        heapq.heappush(pigs_heap, y)
```
### 3.3.4 Shunting Yard
中缀表达式转后缀表达式
```python
def infix_to_postfix(expr):
    priority = {'+': 1, '-': 1, '*': 2, '/': 2}
    stack = []      # 运算符栈
    ans = []        # 后缀表达式
    num = ''        # 数字缓冲区，用于处理多位数和小数
    for ch in expr:
        if ch.isdigit() or ch == '.':
            num += ch
        else:
            if num:
                ans.append(num)
                num = ''
            if ch in '+-*/':
                # 弹出优先级更高或相同的运算符
                while stack and stack[-1] != '(' and priority[stack[-1]] >= priority[ch]:
                    ans.append(stack.pop())
                stack.append(ch)
            elif ch == '(':
                stack.append(ch)
            elif ch == ')':
                # 弹出直到匹配左括号
                while stack and stack[-1] != '(':
                    ans.append(stack.pop())
                stack.pop()  # 弹出左括号
    if num:
        ans.append(num)
    # 剩余运算符全部弹出
    while stack:
        ans.append(stack.pop())
    return ' '.join(ans)
```
  后缀表达式求值
  ```python
  def eval_postfix(expr):
    stack = []
    for token in expr.split():
        if token in '+-*/':
            b = stack.pop()   # 右操作数
            a = stack.pop()   # 左操作数
            if token == '+':
                stack.append(a + b)
            elif token == '-':
                stack.append(a - b)
            elif token == '*':
                stack.append(a * b)
            else:
                stack.append(a / b)
        else:
            stack.append(float(token))
    return stack[-1]
  ```
## 3.4 队列
### 3.4.1 单调队列
常用于滑动窗口最值
```python
from collections import deque
def sliding_window_max(nums, k):
    q = deque()  # 存下标，保证 nums[q] 单调递减
    ans = []
    for i, x in enumerate(nums):
        # 移除已经不在窗口 [i-k+1, i] 内的下标
        while q and q[0] <= i - k:
            q.popleft()
        # 队尾元素小于当前值，不可能再成为最大值
        while q and nums[q[-1]] <= x:
            q.pop()
        q.append(i)
        # 从第一个完整窗口开始记录答案
        if i >= k - 1:
            ans.append(nums[q[0]])
    return ans
```
## 3.5 堆
Python `heapq` 是==小根堆==；大根堆用相反数，懒删除适合“删除任意元素但堆不支持直接删”的题。
```python
import heapq
h = []
heapq.heappush(h, x)
x = heapq.heappop(h)
heapq.heapify(a)

# 大根堆
heapq.heappush(h, -x)
x = -heapq.heappop(h)

# 固定保留最大的 k 个数：堆顶是当前第 k 大
h = []
for x in a:
    if len(h) < k:
        heapq.heappush(h, x)
    elif x > h[0]:
        heapq.heapreplace(h, x)
```
# 4 递归
注意递归深度
递归爆栈可考虑调整递归深度限制：
```python
import sys
sys.setrecursionlimit(10**6)
```
或者将DFS改成迭代就没事了
避免重复计算子问题：
```python
from functools import lru_cache
@lru_cache(maxsize = None)
```
## 4.1 回溯
回溯算法的一般步骤如下面伪代码所示。需要注意以下几点：
- 回溯函数返回值以及参数
- 回溯函数终止条件
- 回溯搜索的遍历过程（横向和纵向）
```python
def backtracking(parameters):
    if ... : #终止条件
        #存放结果
        return
    for ... : #选择本层集合中的元素
        #处理节点
        backtracking(...) #参数一般会包含做过的选择的信息
        #撤销处理结果
```
例：M28906 数的划分
```python
n,k = map(int,input().split())
def dfs(m,t,s):
    if m < s:
        return 0
    if t == 1:
        return 1
    else:
        ans = 0
        for i in range(s,m):
            ans += dfs(m-i,t-1,i)
        return ans
print(dfs(n,k,1))
```
记得考虑能否通过剪枝（去掉不可能的选择）来提高效率。
例：sy134. 组合II
```python
n, k = map(int, input().split())
ls = [int(x) for x in input().split()]
def dfs(k, start, path, res):
    if len(path) == k:
        res.append(path[:])
        return
    for i in range(start, n-(k-len(path))+1):
        # 剪枝：过大的i不可能得到满足条件的组合
        path.append(ls[i])
        dfs(k, i+1, path, res)
        path.pop()
res = []
start = 0
path = []
dfs(k, start, path, res)
for i in res:
    print(*i)
```
# 5 DP
注意以下步骤：
- 确定dp数组以及下标的含义
- 确定递推公式
- dp数组如何初始化
- 确定遍历顺序
- 举例推导dp数组
## 5.0 DP 优化速查
- 滚动数组：当前层只依赖上一层时，二维降一维或两行滚动。
- 状态压缩：集合状态用 `mask`，转移常见 `dp[mask]` 或 `dp[mask][i]`。
- DP + 滑动窗口：转移只依赖最近 `k` 个状态时，用单调队列维护窗口最值。
```python
from collections import deque
# 示例：dp[i] = val[i] + max(dp[j]), i-k <= j < i
q = deque()
for i in range(n):
    while q and q[0] < i - k:
        q.popleft()
    best = dp[q[0]] if q else 0
    dp[i] = val[i] + best
    while q and dp[q[-1]] <= dp[i]:
        q.pop()
    q.append(i)
```
```python
# 状态压缩 DP：遍历集合 mask，尝试加入未选元素
FULL = 1 << n
dp = [10**18] * FULL
dp[0] = 0
for mask in range(FULL):
    for i in range(n):
        if not (mask >> i) & 1:
            nxt = mask | (1 << i)
            dp[nxt] = min(dp[nxt], dp[mask] + cost(mask, i))
ans = dp[FULL - 1]

# 枚举 mask 的非空子集
sub = mask
while sub:
    # 处理 sub
    sub = (sub - 1) & mask
```

```python
# 旅行商问题 TSP：从 0 出发，访问所有点一次后回到 0
# dist[i][j] 表示 i 到 j 的代价；n 一般 <= 20
INF = 10**18
FULL = 1 << n
dp = [[INF] * n for _ in range(FULL)]
dp[1][0] = 0                    # mask=1 表示只访问了 0
for mask in range(FULL):
    if not (mask & 1):           # 固定起点 0 必须在集合里
        continue
    for u in range(n):
        if dp[mask][u] == INF:
            continue
        for v in range(n):
            if (mask >> v) & 1:
                continue
            nxt = mask | (1 << v)
            dp[nxt][v] = min(dp[nxt][v], dp[mask][u] + dist[u][v])
ans = min(dp[FULL - 1][u] + dist[u][0] for u in range(n))  # 回到起点
# 不要求回到起点：ans = min(dp[FULL - 1])
```

## 5.1 背包问题
注意可以进行一些降维优化，避免MLE
```python
# 0-1背包经典模板
def knapsack_01(n, W, w, v):
    dp = [0]*(W+1)
    for i in range(n):
        for j in range(W, w[i]-1,-1):
            dp[j] = max(dp[j], dp[j-w[i]]+v[i])
    return dp[W]
# 完全背包
def knapsack_complete(n, W, w, v):
    dp = [0] * (W + 1)
    for i in range(n):
        for j in range(w[i], W + 1):
            dp[j] = max(dp[j], dp[j - w[i]] + v[i])
    return dp[W]
# 多重背包（每个物品可以选至多k[i]次）
# 思路为将k[i]拆分成0-1背包并用二进制优化
def knapsack_multiple(n, W, w, v, k):
    dp = [0] * (W + 1)
    for i in range(n):
        cnt = k[i]
        base = 1
        while cnt > 0:
            take = min(base, cnt)
            weight = take * w[i]
            value = take * v[i]
            for j in range(W, weight - 1, -1):
                dp[j] = max(dp[j], dp[j - weight] + value)
            cnt -= take
            base <<= 1
    return dp[W]
# 状态压缩
```
也可能是降成二维，即一种滚动数组，例如T30041 不降数组的数量
```python
import bisect
n,m = map(int,input().split())
a = [list(map(int,input().split())) for _ in range(n)]
pre = [i+1 for i in range(m)]
for i in range(1,n):
    cur = [0]*m
    for j in range(m):
        t = bisect.bisect_right(a[i-1],a[i][j])
        if j == 0:
            if t >= 1:
                cur[j] = pre[t-1]
        elif t >= 1:
            cur[j] = cur[j-1]+pre[t-1]
        else:
            cur[j] = cur[j-1]
    pre = cur
print(pre[-1])
```
## 5.2 子序列问题
```python
# 最长上升子序列
n = int(input())
a = list(map(int,input().split()))
dp = [1]*n
for i in range(n):
    for j in range(i):
        if a[j] < a[i]:
            dp[i] = max(dp[i],dp[j]+1)
print(max(dp))
# 最长公共子序列
s = input()
t = input()
m,n = len(s),len(t)
dp = [[0]*n for _ in range(m)]
for i in range(m):
    if s[i] == t[0]:
        dp[i][0] = 1
for j in range(n):
    if s[0] == t[j]:
        dp[0][j] = 1
for i in range(1,m):
    for j in range(1,n):
        if s[i] == t[j]:
            dp[i][j] = dp[i-1][j-1]+1
        else:
            dp[i][j] = max(dp[i-1][j],dp[i][j-1])
print(dp[-1][-1])
```
# 6 图论提示
树和图有单独分册，主表只保留状态 BFS / 0-1 BFS 提醒。
## 6.1 状态 BFS
如果限制会影响后续行动能力，就把限制放入状态维度，例如 `(x, y, used)`、`(x, y, direction)`、`(x, y, time % k)`。
```python
from collections import deque
def bfs(start):
    q = deque([start])
    dist = {start: 0}
    while q:
        state = q.popleft()
        for nxt in next_states(state):
            if nxt not in dist:
                dist[nxt] = dist[state] + 1
                q.append(nxt)
    return dist
```
## 6.2 0-1 BFS
边权只有 `0/1` 时用双端队列，比 Dijkstra 更轻。权重为 0 的边放队首，权重为 1 的边放队尾。
```python
from collections import deque
INF = 10**18
def zero_one_bfs(start, graph):
    dist = [INF] * len(graph)
    dist[start] = 0
    q = deque([start])
    while q:
        u = q.popleft()
        for v, w in graph[u]:          # w 只能是 0 或 1
            nd = dist[u] + w
            if nd < dist[v]:
                dist[v] = nd
                if w == 0:
                    q.appendleft(v)
                else:
                    q.append(v)
    return dist
```
边权全为 1 用 BFS；边权为 0/1 用 0-1 BFS；非负权不等时用 Dijkstra，但仍要保留额外状态维度。
