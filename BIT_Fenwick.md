# 树状数组（Binary Indexed Tree）

*Updated 2026-03-07 19:09 GMT+8*
 *Compiled by Hongfei Yan (2025 Spring)*



> prefix sum 前缀和。
> 
>bit manipulation 位运算。
> 
> E190.颠倒二进制位
>bit manipulation, https://leetcode.cn/problems/reverse-bits/



# 前缀和

## 示例E303.区域和检索 - 数组不可变

prefix sum, https://leetcode.cn/problems/range-sum-query-immutable/

给定一个整数数组  `nums`，处理以下类型的多个查询:

1. 计算索引 `left` 和 `right` （包含 `left` 和 `right`）之间的 `nums` 元素的 **和** ，其中 `left <= right`

实现 `NumArray` 类：

- `NumArray(int[] nums)` 使用数组 `nums` 初始化对象
- `int sumRange(int left, int right)` 返回数组 `nums` 中索引 `left` 和 `right` 之间的元素的 **总和**，包含 `left` 和 `right` 两点（也就是 `nums[left] + nums[left + 1] + ... + nums[right]` )

 

**示例 1：**

```
输入：
["NumArray", "sumRange", "sumRange", "sumRange"]
[[[-2, 0, 3, -5, 2, -1]], [0, 2], [2, 5], [0, 5]]
输出：
[null, 1, -1, -3]

解释：
NumArray numArray = new NumArray([-2, 0, 3, -5, 2, -1]);
numArray.sumRange(0, 2); // return 1 ((-2) + 0 + 3)
numArray.sumRange(2, 5); // return -1 (3 + (-5) + 2 + (-1)) 
numArray.sumRange(0, 5); // return -3 ((-2) + 0 + 3 + (-5) + 2 + (-1))
```

 

**提示：**

- `1 <= nums.length <= 10^4`
- `-10^5 <= nums[i] <= 10^5`
- `0 <= left <= right < nums.length`
- 最多调用 `10^4` 次 `sumRange` 方法





如果只是 **查询一次** 或 **很少查询**，可以直接：

```python
sum(nums[left:right+1])
```

但如果：查询次数 ≥ n，就必须 **前缀和**。这也是这题为什么叫 **Range Query**。



这题是经典 **前缀和（Prefix Sum）** 模板题。思路很直接：

**一、核心思想**

预处理一个 **前缀和数组** `pre`：
pre[i] = nums[0] + nums[1] + ... + nums[i-1]

也就是：

```
pre[0] = 0
pre[1] = nums[0]
pre[2] = nums[0] + nums[1]
...
```

这样区间 `[left, right]` 的和就是：
sum(left,right) = pre[right+1] - pre[left]

原因：

```
pre[right+1] = nums[0] + ... + nums[right]
pre[left]    = nums[0] + ... + nums[left-1]
```

相减就得到：

```
nums[left] + ... + nums[right]
```

时间复杂度：

- 初始化：`O(n)`
- 每次查询：`O(1)`

非常适合 **多次查询区间和**。

------

**二、Python实现**

```python
from typing import List


class NumArray:

    def __init__(self, nums: List[int]):
        n = len(nums)
        self.pre = [0] * (n + 1)

        for i in range(n):
            self.pre[i + 1] = self.pre[i] + nums[i]

    def sumRange(self, left: int, right: int) -> int:
        return self.pre[right + 1] - self.pre[left]

# Your NumArray object will be instantiated and called as such:
# obj = NumArray(nums)
# param_1 = obj.sumRange(left,right)
```

------

**四、为什么 `pre` 长度是 `n+1`**

关键是 **避免特判 left=0**。

如果不用 `n+1`，写法会变成：

```python
if left == 0:
    return pre[right]
else:
    return pre[right] - pre[left-1]
```

而使用 `n+1`：pre[0] = 0

就可以统一写成：pre[right+1] - pre[left]

代码更干净。

------

**五、这题的本质**

这是 **最基础的前缀和模型**：

| 问题类型       | 技巧                   |
| -------------- | ---------------------- |
| 多次区间求和   | 前缀和                 |
| 区间修改       | 差分                   |
| 动态更新区间和 | Fenwick / Segment Tree |

这题本质就是：预处理 O(n)，查询 O(1)



常用的前缀和写法**，比标准写法更 **短、清晰、Pythonic。

------

**三行构造前缀和（推荐写法）**

核心技巧：
利用 **`itertools.accumulate`**

```python
from typing import List
import itertools

class NumArray:

    def __init__(self, nums: List[int]):
        self.pre = [0] + list(itertools.accumulate(nums))

    def sumRange(self, left: int, right: int) -> int:
        return self.pre[right+1] - self.pre[left]
```

初始化只需要 **一行核心代码**：



**四、最常见的前缀和模板**

以后你在做算法题基本都会看到这个模板：

```python
from itertools import accumulate

nums = [1,2,3,4,5]

pre = [0] + list(accumulate(nums))

# 区间和
def query(l,r):
    return pre[r+1] - pre[l]
```

------

**五、很多人不知道的一个技巧**

`accumulate` 还能做 **前缀最大值 / 最小值 / 乘积**：

**前缀最大值**

```python
from itertools import accumulate
import operator

nums = [3,1,5,2,4]

pre_max = list(accumulate(nums, max))
```

得到：

```
[3,3,5,5,5]
```

------

**前缀乘积**

```python
list(accumulate(nums, operator.mul))
```



### 顺便掌握：示例M304.二维区域和检索 - 矩阵不可变

prefix sum, https://leetcode.cn/problems/range-sum-query-2d-immutable/

给定一个二维矩阵 `matrix`，以下类型的多个请求：

- 计算其子矩形范围内元素的总和，该子矩阵的 **左上角** 为 `(row1, col1)` ，**右下角** 为 `(row2, col2)` 。

实现 `NumMatrix` 类：

- `NumMatrix(int[][] matrix)` 给定整数矩阵 `matrix` 进行初始化
- `int sumRegion(int row1, int col1, int row2, int col2)` 返回 **左上角** `(row1, col1)` 、**右下角** `(row2, col2)` 所描述的子矩阵的元素 **总和** 。

 

**示例 1：**

<img src="https://raw.githubusercontent.com/GMyhf/img/main/img/202603072305356.png" alt="img" style="zoom:50%;" />

```
输入: 
["NumMatrix","sumRegion","sumRegion","sumRegion"]
[[[[3,0,1,4,2],[5,6,3,2,1],[1,2,0,1,5],[4,1,0,1,7],[1,0,3,0,5]]],[2,1,4,3],[1,1,2,2],[1,2,2,4]]
输出: 
[null, 8, 11, 12]

解释:
NumMatrix numMatrix = new NumMatrix([[3,0,1,4,2],[5,6,3,2,1],[1,2,0,1,5],[4,1,0,1,7],[1,0,3,0,5]]);
numMatrix.sumRegion(2, 1, 4, 3); // return 8 (红色矩形框的元素总和)
numMatrix.sumRegion(1, 1, 2, 2); // return 11 (绿色矩形框的元素总和)
numMatrix.sumRegion(1, 2, 2, 4); // return 12 (蓝色矩形框的元素总和)
```

 

**提示：**

- `m == matrix.length`
- `n == matrix[i].length`



**构造公式**

二维前缀和：

```
pre[i][j] =
  pre[i-1][j]
  + pre[i][j-1]
  - pre[i-1][j-1]
  + matrix[i-1][j-1]
```

图形理解：

```
       j
   ┌───────┐
 i │   A   │
   │       │
   │       │
   └───────┘
```

计算 `pre[i][j]` 时：上方 + 左方 - 重复区域 + 当前元素

因为：`pre[i-1][j]` 和 `pre[i][j-1]` 重复算了 `pre[i-1][j-1]`，所以要减一次。

------

**O(1) 查询公式**

查询：(r1,c1) 到 (r2,c2)

公式：

```
sum =
  pre[r2+1][c2+1]
  - pre[r1][c2+1]
  - pre[r2+1][c1]
  + pre[r1][c1]
```

图示：

```
        c1      c2
        │       │
     ┌──┼───────┼──┐
     │  │       │  │
 r1 ─┼──A───────B──┤
     │  │       │  │
     │  │ query │  │
     │  │       │  │
 r2 ─┼──C───────D──┤
     │  │       │  │
     └──┴───────┴──┘
```

计算：D - B - C + A



```python
from typing import List

class NumMatrix:

    def __init__(self, matrix: List[List[int]]):

        n = len(matrix)
        m = len(matrix[0])

        self.pre = [[0]*(m+1) for _ in range(n+1)]

        for i in range(1,n+1):
            for j in range(1,m+1):
                self.pre[i][j] = (
                    self.pre[i-1][j]
                    + self.pre[i][j-1]
                    - self.pre[i-1][j-1]
                    + matrix[i-1][j-1]
                )

    def sumRegion(self, r1: int, c1: int, r2: int, c2: int) -> int:

        return (
            self.pre[r2+1][c2+1]
            - self.pre[r1][c2+1]
            - self.pre[r2+1][c1]
            + self.pre[r1][c1]
        )
        

# Your NumMatrix object will be instantiated and called as such:
# obj = NumMatrix(matrix)
# param_1 = obj.sumRegion(row1,col1,row2,col2)
```





## M307.区域和检索 - 数组可修改？

binary indexed tree, segment tree, https://leetcode.cn/problems/range-sum-query-mutable/

给你一个数组 `nums` ，请你完成两类查询。

1. 其中一类查询要求 **更新** 数组 `nums` 下标对应的值
2. 另一类查询要求返回数组 `nums` 中索引 `left` 和索引 `right` 之间（ **包含** ）的nums元素的 **和** ，其中 `left <= right`

实现 `NumArray` 类：

- `NumArray(int[] nums)` 用整数数组 `nums` 初始化对象
- `void update(int index, int val)` 将 `nums[index]` 的值 **更新** 为 `val`
- `int sumRange(int left, int right)` 返回数组 `nums` 中索引 `left` 和索引 `right` 之间（ **包含** ）的nums元素的 **和** （即，`nums[left] + nums[left + 1], ..., nums[right]`）

 

**示例 1：**

```
输入：
["NumArray", "sumRange", "update", "sumRange"]
[[[1, 3, 5]], [0, 2], [1, 2], [0, 2]]
输出：
[null, 9, null, 8]

解释：
NumArray numArray = new NumArray([1, 3, 5]);
numArray.sumRange(0, 2); // 返回 1 + 3 + 5 = 9
numArray.update(1, 2);   // nums = [1,2,5]
numArray.sumRange(0, 2); // 返回 1 + 2 + 5 = 8
```

 

**提示：**

- `1 <= nums.length <= 3 * 10^4`
- `-100 <= nums[i] <= 100`
- `0 <= index < nums.length`
- `-100 <= val <= 100`
- `0 <= left <= right < nums.length`
- 调用 `update` 和 `sumRange` 方法次数不大于 `3 * 10^4` 



```python
lass NumArray:

    def __init__(self, nums: List[int]):
        

    def update(self, index: int, val: int) -> None:
        

    def sumRange(self, left: int, right: int) -> int:
        


# Your NumArray object will be instantiated and called as such:
# obj = NumArray(nums)
# obj.update(index,val)
# param_2 = obj.sumRange(left,right)
```



> 灵神讲的很清楚，证明我没看。里面还有视频讲解的链接。
>
> 带你发明树状数组！附数学证明
> https://leetcode.cn/problems/range-sum-query-mutable/solutions/2524481/dai-ni-fa-ming-shu-zhuang-shu-zu-fu-shu-lyfll/
>
> >把一个正整数拆分，按照2的幂，从右往左拆分。拆分出的关键区间个数，是二进制数中1的个数是。位运算技巧。
>
> 
>
> ![image-20260308021201663](https://raw.githubusercontent.com/GMyhf/img/main/img/202603080212764.png)
>
> 
>
> 关于树状数组，重在理解其原理与应用，掌握其核心思想即可，无需深究形式化证明。这一数据结构设计精妙，其中对位运算的巧妙运用堪称点睛之笔，充分体现了算法的优雅与高效。值得一提的是，在利用 lowbit 实现前缀和查询时，常见的写法 i -= i & -i 可以等价地改写为 i &= i - 1。两者语义完全相同，但后者更优——不仅代码更简洁，还少了一次算术运算，效率略高。
>
> 它的核心思想是将数组按特定规则分组进行高效检索：利用整数的二进制表示，将其按 2 的幂次进行划分，从而实现对前缀信息的快速维护与查询。这一设计仅需 O(log n) 的时间复杂度，构思巧妙，堪称天才之作。



这个问题要求实现一个支持“单点修改”和“区域检索”的数据结构。

对于此类问题，普通的数组实现中：

*   **数组/前缀和**：`update` 是 $O(n)$，`sumRange` 是 $O(1)$。
*   **普通数组**：`update` 是 $O(1)$，`sumRange` 是 $O(n)$。

当两者调用次数都很多时（本题均为 $3 \times 10^4$），需要更高效的数据结构。最常用的两种方案是：**树状数组 (Binary Indexed Tree / Fenwick Tree)** 和 **线段树 (Segment Tree)**。

---

### 树状数组 (Binary Indexed Tree)

树状数组是处理“动态前缀和”最简洁的工具。其核心思想是利用二进制低位的 `lowbit` 来管理不同长度的区间和。

*   **时间复杂度**：
    *   初始化：$O(n)$ 或 $O(n \log n)$
    *   `update`：$O(\log n)$
    *   `sumRange`：$O(\log n)$
*   **空间复杂度**：$O(n)$

```python
class NumArray:
    def __init__(self, nums: List[int]):
        self.nums = nums
        self.n = len(nums)
        # tree 数组下标从 1 开始，所以长度为 n + 1
        self.tree = [0] * (self.n + 1)
        # 初始化树状数组
        for i, val in enumerate(nums):
            self._add(i + 1, val)

    def _lowbit(self, x: int) -> int:
        return x & -x

    def _add(self, index: int, delta: int):
        """在树状数组的 index 位置增加 delta"""
        while index <= self.n:
            self.tree[index] += delta
            index += self._lowbit(index)

    def _query(self, index: int) -> int:
        """查询前缀和 [0...index]"""
        res = 0
        while index > 0:
            res += self.tree[index]
            index -= self._lowbit(index)
        return res

    def update(self, index: int, val: int) -> None:
        # 计算增量 delta
        delta = val - self.nums[index]
        self.nums[index] = val
        # 树状数组内部是 1-indexed
        self._add(index + 1, delta)

    def sumRange(self, left: int, right: int) -> int:
        # sum(left, right) = query(right) - query(left - 1)
        return self._query(right + 1) - self._query(left)
```





# 动态前缀和：树状数组

树状数组或二叉索引树（英语：Binary Indexed Tree），又以其发明者命名为Fenwick树，最早由Peter M. Fenwick于1994年以A New Data Structure for Cumulative Frequency Tables为题发表。其初衷是解决数据压缩里的累积频率（Cumulative Frequency）的计算问题，现多用于高效计算数列的<mark>前缀和， 区间和</mark>。

一般来说，如果在查询的过程中元素可能发生改变（例如插入、修改或删除），就称这种查询为<mark>在线查询</mark>;如果在查询过程中元素不发生改变，就称为**离线查询**。



> 二叉索引树（树状数组）用于处理对固定大小的数组进行以下多种操作的这类问题。
>
> - 前缀操作（求和、求积、异或、按位或等）。注意，区间操作也可以通过前缀来解决。例如，从索引L到R的区间和等于到R（包含R）的前缀和减去到L - 1的前缀和。
> - 更新数组中的一个元素
>
> 这两种操作的时间复杂度均为$O(logN)$。注意，我们需要$O(NlogN)$的预处理时间和$O(N)$的辅助空间。
>
> 
>
> 让我们考虑以下问题来理解二叉索引树（Binary Indexed Tree, BIT）：
> 我们有一个数组 $arr[0 . . . n-1]$。我们希望实现两个操作：
>
> 1. 计算前i个元素的和。
> 2. 修改数组中指定位置的值，即设置 $arr[i] = x$，其中 $0 \leq i \leq n-1$。
>
> 一个简单的解决方案是从 0 到 i-1 遍历并计算这些元素的总和。要更新一个值，只需执行 $arr[i] = x$。第一个操作的时间复杂度为$O(N)$，而第二个操作的时间复杂度为$O(1)$。另一种简单的解决方案是创建一个额外的数组，并在这个新数组的第i个位置存储前i个元素的总和。这样，给定范围的和可以在$O(1)$时间内计算出来，但是更新操作现在需要$O(N)$时间。当查询操作非常多而更新操作非常少时，这种方法表现良好。
>
> **我们能否在$O(log N)$时间内同时完成查询和更新操作呢？**
> 一种高效的解决方案是使用段树（Segment Tree），它能够在$O(logN)$时间内完成这两个操作。
> 另一种解决方案是二叉索引树（Binary Indexed Tree，也称作Fenwick Tree），同样能够以$O(logN)$的时间复杂度完成查询和更新操作。与段树相比，二叉索引树所需的空间更少，且实现起来更加简单。



### lowbit 运算

二进制中一个经典应用是 lowbit 运算，即 `lowbit(x) = x & (-x)`。

**整数的二进制表示常用的方式之一是使用补码**

补码是一种表示有符号整数的方法，它将负数的二进制表示转换为正数的二进制表示。补码的优势在于可以使用相同的算术运算规则来处理正数和负数，而不需要特殊的操作。

在补码表示中，最高位用于表示符号位，0表示正数，1表示负数。其他位表示数值部分。

具体将一个整数转换为补码的步骤如下：

1. 如果整数是正数，则补码等于二进制表示本身。
2. 如果整数是负数，则需要先将其绝对值转换为二进制，然后取反，最后加1。等价于<mark>把二进制最右边的1的左边的每一位都取反</mark>。

例如，假设要将 -12 转换为补码：

1. 12的二进制表示为00001100。

2. 将其取反得到11110011。

3. 加1得到11110100，这就是 -12 的补码表示。


通过`lowbit(x) = x & (-x)`就是取 x 的二进制最右边的1和它右边所有的0，因此它一定是2的幂次，即1、2、4、8等。

对 x = 12 = $(00001100)_2$，有 -x = $(11110100)_2$ ，x & (-x) = 4

对 x= 6 = $(110)_2$，有 -x = $(010)_2$，x & (-x) = 2



### 表示方式

树状数组（Binary Indexed Tree，BIT）用数组形式表示。它其实仍然是一个数组，并且与 sum 数组类似，是一个用来记录和的数组，只不过它存放的不是前 i 个整数之和，而是在 <mark>i 号位之前（含i号位）lowbit(i) 个整数之和</mark>。树状数组的大小等于输入数组的大小，记为n。在下面的代码中，为了便于实现，使用n+1的大小。

如下图 所示，数组A是原始数组，有 A[1]~ A[16]共 16个元素；数组 C是树状数组，其中 C[i]存放数组 A 中i号位之前 lowbit(i) 个元素之和。显然，<mark>C[i]的覆盖长度是 lowbit(i)（也可以理解成管辖范围）</mark>，它是2的幂次，即 1、2、4、8等。

需要注意的是，树状数组仍旧是一个平坦的数组，画成树形是为了让存储的元素更容易观察。

<img src="https://raw.githubusercontent.com/GMyhf/img/main/img/image-20250320134426632.png" alt="image-20250320134426632" style="zoom: 67%;" />

<center>图 树状数组定义图</center>



```
C[1] = A[1]  													(长度为 lowbit(1) = 1) 
C[2] = A[1] + A[2]  									(长度为 lowbit(2) = 2) 
C[3] = A[3]  													(长度为 lowbit(3) = 1) 
C[4] = A[1] + A[2] + A[3] + A[4]  		(长度为 lowbit(4) = 4) 
C[5] = A[5]  													(长度为 lowbit(5) = 1) 
C[6] = A[5] + A[6]  									(长度为 lowbit(6) = 2) 
C[7] = A[7]  													(长度为 lowbit(7) = 1) 
C[8] = A[1] + A[2] + A[3] + A[4] + A[5] + A[6] + A[7] + A[8]  (长度为 lowbit(8) = 8) 
```

<mark>树状数组的定义非常重要，特别是“C[i]的覆盖长度是 lowbit(i)”这点；另外，树状数组的下标必须从1开始</mark>。接下来思考一下，在这样的定义下，
怎样解决下面两个问题：

① 设计函数 get_sum(x)，返回前x个数之和 A[1]+...+ A[x]。

② 设计函数 update_bit(x,v)，实现将第x个数加上一个数v的功能，即 A[x]+= v。

先来看第一个问题，即如何设计函数 get_sum(x)，返回前x个数之和。不妨先看个例子。假设想要查询 A[1]+…+A[14]，那么从树状数组的定义出发，它实际是什么东西呢? 回到上图，很容易发现 A[1]+…+A[14] = C[8]+C[12]+ C[14]。又比如要査询 A[1]+…A[11]，从图中同样可以得到 A[1]+…+A[11] = C[8]+C[10]+ C[11]。那么怎样知道 A[1]+…+ A[x]对应的是树状数组中的哪些项呢？事实上这很简单。记 SUM(1,x) = A[1]+……+A[x]，由于 C[x]的覆盖长度是 lowbit(x)，因此

C[x] = A[x-lowbit(x)+1]+...+ A[x]

于是可以得到

```
SUM(1,x) = A[1] +···+ A[x]
				=A[1] +···+ A[x-lowbit(x)] + A[x-lowbit(x)+1] +···+ A[x]
				=SUM(1,x-lowbit(x)) + C[x]
```

这样就把 SUM(1,x)转换为 SUM(1,x-lowbit(x))了。

接着就能写出 get_sum 函数了，其中BITTree是树状数组。

```python
def bit_sum(BIT, i):
    s = 0
    i += 1  # index in BIT[] is 1 more than the index in arr[]

    while i > 0:  # Traverse ancestors of BIT[index]
        s += BIT[i]
        i -= i & (-i)  # Move index to parent node
    return s
```

由于 lowbit(i)的作用是定位i的二进制中最右边的1，因此 `i = i- lowbit(i)` 事实上是不断把i的二进制中最右边的1置为0的过程。所以 get_sum 函数的 for 循环执行次数为x的二进制中1的个数。一个数n的二进制表示中设置位的数量是O(logn)。也就是说，get_sum 函数的时间复杂度为 $O(logN)$。从另一个角度理解,结合图会发现，get_sum 函数的过程实际上是在沿着一条不断左上的路径行进（可以想一想 get_sum(14)跟 get_sum(11)的过程）。于是由于“树”高是 $O(logN)$级别,因此可以同样得到 get_sum 函数的时间复杂度就是 $O(logN)$。另外，<mark>如果要求数组下标在区间[x,y]内的数之和，即 A[x] + A[x+1] +…+ A[y]，可以转换成 get_sum(y) - get_sum(x-1)来解决，这是一个很重要的技巧</mark>。



接着来看第二个问题，即如何设计函数 update(x,v)，实现将第x个数加上一个数v的功
能。
来看两个例子。假如要让 A[6]加上一个数 v，那么就要寻找树状数组C中能覆盖了 A[6]的元素，让它们都加上 v。也就是说，如果要让 A[6]加上 v，实际上是要让C[6]、C[8]、C[16]都加上 v。同样，如果要将 A[9]加上一个数 v,实际上就是要让 C[9]、C[10]、C[12]、C[16]都加上 v。于是问题又来了——想要给 A[x]加上v时，怎样去寻找树状数组中的对应项呢?

要让 A[x]加上 v，就是要寻找树状数组 C 中能覆盖 A[x]的那些元素，让它们都加上 v。而从图 1中直观地看，只需要总是寻找离当前的“矩形”C[x]最近的“矩形”C[y]，使得 C[y]能够覆盖 C[x]即可。例如要让 A[6]加上 v，就从 C[6]开始找起：离 C[6]最近的能覆盖 C[6]的“矩形”是 C[8]，离 C[8]最近的能覆盖 C[8]的“矩形”是 C[16]，于是只要把 C[6]、C[8]、C[16]都加上v即可。

那么，如何找到距离当前的 C[x]最近的能覆盖 C[x]的 C[y]呢？首先，可以得到一个显然的结论：lowbit(y)必须大于 lowbit(x)（不然怎么覆盖呢……）。于是问题等价于求一个尽可能小的整数 a，使得 lowbit(x+a)>lowbit(x)。显然，由于 lowbit(x)是取x的二进制最右边的1的位置，因此如果 lowbit(a) < lowbit(x)，lowbit(x+ a)就会小于 lowbit(x)。为此 lowbit(a)必须不小于 lowbit(x)。接着发现，当a取 lowbit(x)时，由于x和a的二进制最右边的1的位置相同,因此x+a会在这个1的位置上产生进位，使得进位过程中的所有连续的1变成0，直到把它们左边第一个0置为1时结束。于是lowbit(x+a)>lowbit(x)显然成立,最小的a就是lowbit(x)。于是 update 函数的做法就很明确了，只要让x不断加上 lowbit(x)，并让每步的 C[x]都加上 v，直到x超过给定的数据范围为止。代码如下：

```python
def bit_update(BIT, n, i, v):
    i += 1  # index in BITree[] is 1 more than the index in arr[]

    while i <= n:  # Traverse all ancestors and add 'val'
        BIT[i] += v
        i += i & (-i)  # Update index to that of parent
```

更新函数需要确保所有包含arr[i]在其范围内的BIT节点都被更新。我们通过不断向当前索引添加其最后一位设置位对应的十进制数，在BIT中循环遍历这些节点。



### **实现** 

首先将BIT[]中的所有值初始化为0。然后对所有的索引调用bit_update()函数。

```python
# Binary Indexed Tree

def bit_sum(BIT, i):
    """计算树状数组 BIT 从索引 1 到 i 的前缀和"""
    s = 0
    while i > 0:
        s += BIT[i]
        i -= i & (-i)  # 回溯至祖先节点
    return s


def bit_update(BIT, i, v):
    """在树状数组 BIT 中更新索引 i 处的值 v"""
    while i < len(BIT):
        BIT[i] += v
        i += i & (-i)  # 回溯至祖先节点


# Constructs and returns a Binary Indexed Tree for given array of size n.
def construct(arr, n):
    BIT = [0] * (n + 1)
    for i in range(n):  # Store the actual values in BIT[] using bit_update()
        bit_update(BIT, i + 1, arr[i])

    return BIT


arr = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16]
BIT = construct(arr, len(arr))
print(f'BIT: ', *BIT)
print("Sum of elements in arr[0..5] is " + str(bit_sum(BIT, 5)))
arr[3] += 6
bit_update(BIT, 3, 6)
print(f'BIT: ', *BIT)
print("Sum of elements in arr[0..5]" +
      " after update is " + str(bit_sum(BIT, 5)))

```

**Output**

```
BIT:  0 1 3 3 10 5 11 7 36 9 19 11 42 13 27 15 136
Sum of elements in arr[0..5] is 15
BIT:  0 1 3 9 16 5 11 7 42 9 19 11 42 13 27 15 142
Sum of elements in arr[0..5] after update is 21
```

**Time Complexity:** $O(NlogN)$
**Auxiliary Space:** $O(N)$

**Can we extend the Binary Indexed Tree to computing the sum of a range in O(Logn) time?** 
Yes. rangeSum(l, r) = get_sum(r) – get_sum(l-1).

**References:** 
http://en.wikipedia.org/wiki/Fenwick_tree 



### 示例20018:蚂蚁王国的越野跑

BIT, http://cs101.openjudge.cn/practice/20018/

为了促进蚂蚁家族身体健康，提高蚁族健身意识，蚂蚁王国举行了越野跑。假设越野跑共有N个蚂蚁参加，在一条笔直的道路上进行。N个蚂蚁在起点处站成一列，相邻两个蚂蚁之间保持一定的间距。比赛开始后，N个蚂蚁同时沿着道路向相同的方向跑去。换句话说，这N个蚂蚁可以看作x轴上的N个点，在比赛开始后，它们同时向X轴正方向移动。假设越野跑的距离足够远，这N个蚂蚁的速度有的不相同有的相同且保持匀速运动，那么会有多少对参赛者之间发生“赶超”的事件呢？此题结果比较大，需要定义long long类型。请看备注。

<img src="https://raw.githubusercontent.com/GMyhf/img/main/img/202603080219860.png" alt="27f2cd6ea801d4e18725b508f09b66ac" style="zoom:33%;" />



**输入**

第一行1个整数N。
第2… N +1行：N 个非负整数，按从前到后的顺序给出每个蚂蚁的跑步速度。对于50%的数据，2<=N<=1000。对于100%的数据，2<=N<=100000。

输出

一个整数，表示有多少对参赛者之间发生赶超事件。

样例输入

```
5
1
5
10
7
6

5
1
5
5
7
6
```

样例输出

```
7

8
```

提示

我们把这5个蚂蚁依次编号为A,B,C,D,E，假设速度分别为1,5,5,7,6。在跑步过程中：B,C,D,E均会超过A，因为他们的速度都比A快；D,E都会超过B,C，因为他们的速度都比B,C快；D,E之间不会发生赶超，因为速度快的起跑时就在前边；B,C之间不会发生赶超，因为速度一样，在前面的就一直在前面。

考虑归并排序的思想。

此题结果比较大，需要定义long long类型，其输出格式为printf("%lld",x);
long long，有符号 64位整数，所占8个字节(Byte)
-9,223,372,036,854,775,808 to 9,223,372,036,854,775,807



```python
# 张清州 24化学学院
def bit_sum(BIT, i):
    """计算树状数组 BIT 从索引 1 到 i 的前缀和"""
    s = 0
    while i > 0:
        s += BIT[i]
        i -= i & (-i)  # 回溯至祖先节点
    return s


def bit_update(BIT, i, v):
    """在树状数组 BIT 中更新索引 i 处的值 v"""
    while i < len(BIT):
        BIT[i] += v
        i += i & (-i)  # 回溯至祖先节点


# 读取输入并进行离散化
n = int(input())
values = [int(input()) for _ in range(n)]

# 离散化：建立值到索引的映射
sorted_vals = sorted(set(values))
value_to_index = {v: i + 1 for i, v in enumerate(sorted_vals)}

# 初始化树状数组
BIT = [0] * (len(sorted_vals) + 1)
count = 0

# 计算逆序对
for v in values:
    index = value_to_index[v]
    count += bit_sum(BIT, index - 1)  # 查询比当前值小的元素个数
    bit_update(BIT, index, 1)  # 在树状数组中记录当前值出现次数

print(count)
```

