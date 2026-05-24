# LeetCode Top 100 Liked 题解

> 来源：https://leetcode.cn/studyplan/top-100-liked/
>
> 每题包含：题目编号、思路分析、Python代码实现（含关键注释）。

---

## 目录（按专题分类）

1. [哈希表](#一哈希表)
2. [双指针](#二双指针)
3. [滑动窗口](#三滑动窗口)
4. [子串/子数组](#四子串子数组)
5. [普通数组](#五普通数组)
6. [矩阵](#六矩阵)
7. [链表](#七链表)
8. [二叉树](#八二叉树)
9. [二叉搜索树](#九二叉搜索树)
10. [二分查找](#十分查找)
11. [栈](#十一栈)
12. [堆](#十二堆)
13. [贪心](#十三贪心)
14. [回溯](#十四回溯)
15. [动态规划](#十五动态规划)
16. [图论](#十六图论)
17. [设计/其他](#十七设计其他)

---

## 一、哈希表

### E1. Two Sum（两数之和）✅

给定一个整数数组 `nums` 和一个整数目标值 `target`，请你在该数组中找出 **和为目标值** *`target`* 的那 **两个** 整数，并返回它们的数组下标。

你可以假设每种输入只会对应一个答案，并且你不能使用两次相同的元素。

你可以按任意顺序返回答案。



**思路**：遍历数组，用哈希表记录每个数对应的下标。对于当前数 `nums[i]`，检查 `target - nums[i]` 是否已在哈希表中。

```python
def twoSum(nums, target):
    seen = {}
    for i, num in enumerate(nums):
        complement = target - num
        if complement in seen:
            return [seen[complement], i]
        seen[num] = i
```



### M49. Group Anagrams（字母异位词分组）✅

给你一个字符串数组，请你将 字母异位词 组合在一起。可以按任意顺序返回结果列表。



**思路**：将每个字符串排序作为 key，存入字典。同一 key 的字符串即为异位词。

```python
def groupAnagrams(strs):
    from collections import defaultdict
    groups = defaultdict(list)
    for s in strs:
        key = ''.join(sorted(s))
        groups[key].append(s)
    return list(groups.values())
```



### M128. Longest Consecutive Sequence（最长连续序列）✅

给定一个未排序的整数数组 `nums` ，找出数字连续的最长序列（不要求序列元素在原数组中连续）的长度。

请你设计并实现时间复杂度为 `O(n)` 的算法解决此问题。



**思路**：用集合存所有数。遍历每个数，若 `num - 1` 不在集合中，则以此为起点向后连续查找。

```python
def longestConsecutive(nums):
    s, longest = set(nums), 0
    for num in s:
        if num - 1 not in s:
            cur, length = num, 1
            while cur + 1 in s:
                cur += 1
                length += 1
            longest = max(longest, length)
    return longest
```

### 560. Subarray Sum Equals K（和为 K 的子数组）

**思路**：前缀和 + 哈希表。`sum[i] - sum[j] == k` 等价于 `sum[j] == sum[i] - k`。

```python
def subarraySum(nums, k):
    prefix, s, ans = {0: 1}, 0, 0
    for num in nums:
        s += num
        ans += prefix.get(s - k, 0)
        prefix[s] = prefix.get(s, 0) + 1
    return ans
```

### 136. Single Number（只出现一次的数字）

**思路**：异或运算——相同数异或为 0，0 异或任何数等于其本身。

```python
def singleNumber(nums):
    res = 0
    for num in nums:
        res ^= num
    return res
```

---

## 二、双指针

### M11. Container With Most Water（盛最多水的容器）✅

给定一个长度为 `n` 的整数数组 `height` 。有 `n` 条垂线，第 `i` 条线的两个端点是 `(i, 0)` 和 `(i, height[i])` 。

找出其中的两条线，使得它们与 `x` 轴共同构成的容器可以容纳最多的水。

返回容器可以储存的最大水量。

**说明：**你不能倾斜容器。



**思路**：左右指针向中间移动。每次移动高度较小的那一边，计算并更新最大面积。

```python
def maxArea(height):
    l, r, ans = 0, len(height) - 1, 0
    while l < r:
        ans = max(ans, min(height[l], height[r]) * (r - l))
        if height[l] < height[r]:
            l += 1
        else:
            r -= 1
    return ans
```

### T42. Trapping Rain Water（接雨水）✅

给定 `n` 个非负整数表示每个宽度为 `1` 的柱子的高度图，计算按此排列的柱子，下雨之后能接多少雨水。



**思路**：双指针记录左右最大高度。哪边矮就处理哪一边，当前位置能接的雨水 = min(left_max, right_max) - height[i]。

**“木桶效应”**（又称短板效应）是指：一只木桶想装满水，它能装多少水并不取决于最长的那块木板，而是取决于**最短的那块木板**。

```python
def trap(height):
    l, r = 0, len(height) - 1
    left_max = right_max = ans = 0
    
    while l < r:
        if height[l] < height[r]:
            # 情况 A：左边柱子较矮
            if height[l] >= left_max:
                # 如果当前高度大于或等于历史左侧最大高度，说明存不住水，更新 left_max
                left_max = height[l]
            else:
                # 否则，当前位置可以接水，水量为 left_max 与当前高度的差值
                ans += left_max - height[l]
            # 左指针向右移动
            l += 1
        else:
            # 情况 B：右边柱子较矮（或等高）
            if height[r] >= right_max:
                # 如果当前高度大于或等于历史右侧最大高度，更新 right_max
                right_max = height[r]
            else:
                # 否则，当前位置可以接水，水量为 right_max 与当前高度的差值
                ans += right_max - height[r]
            # 右指针向左移动
            r -= 1
            
    return ans
```

### M15. 3Sum（三数之和）✅

给你一个整数数组 `nums` ，判断是否存在三元组 `[nums[i], nums[j], nums[k]]` 满足 `i != j`、`i != k` 且 `j != k` ，同时还满足 `nums[i] + nums[j] + nums[k] == 0` 。请你返回所有和为 `0` 且不重复的三元组。

**注意：**答案中不可以包含重复的三元组。



**思路**：排序后固定第一个数，双指针找另外两个数。注意跳过重复元素。

```python
def threeSum(nums):
    nums.sort()
    res = []
    for i in range(len(nums) - 2):
        if i > 0 and nums[i] == nums[i - 1]:
            continue
        l, r = i + 1, len(nums) - 1
        while l < r:
            s = nums[i] + nums[l] + nums[r]
            if s < 0:
                l += 1
            elif s > 0:
                r -= 1
            else:
                res.append([nums[i], nums[l], nums[r]])
                while l < r and nums[l] == nums[l + 1]:
                    l += 1
                while l < r and nums[r] == nums[r - 1]:
                    r -= 1
                l += 1
                r -= 1
    return res
```



### 75. Sort Colors（颜色分类）

**思路**：三指针（荷兰国旗问题）。用 `p0` 指向 0 的右边界，`p2` 指向 2 的左边界，`i` 遍历数组。

```python
def sortColors(nums):
    p0, i, p2 = 0, 0, len(nums) - 1
    while i <= p2:
        if nums[i] == 0:
            nums[i], nums[p0] = nums[p0], nums[i]
            p0 += 1
            i += 1
        elif nums[i] == 2:
            nums[i], nums[p2] = nums[p2], nums[i]
            p2 -= 1
        else:
            i += 1
```

### E283. Move Zeroes（移动零）✅

给定一个数组 `nums`，编写一个函数将所有 `0` 移动到数组的末尾，同时保持非零元素的相对顺序。

**请注意** ，必须在不复制数组的情况下原地对数组进行操作。



**思路**：用一个指针 `pos` 记录非零元素应该放置的位置。遍历数组，将非零元素前移，最后补零。

```python
def moveZeroes(nums):
    pos = 0
    for i in range(len(nums)):
        if nums[i] != 0:
            nums[pos], nums[i] = nums[i], nums[pos]
            pos += 1
```

### 287. Find the Duplicate Number（寻找重复数）

**思路**：将数组看作链表（值指向下标），用快慢指针找环的入口即重复数。

```python
def findDuplicate(nums):
    slow = fast = nums[0]
    while True:
        slow = nums[slow]
        fast = nums[nums[fast]]
        if slow == fast:
            break
    slow = nums[0]
    while slow != fast:
        slow = nums[slow]
        fast = nums[fast]
    return slow
```

---

## 三、滑动窗口

### 3. Longest Substring Without Repeating Characters（无重复字符的最长子串）

**思路**：用哈希集合记录窗口内字符。右指针扩展窗口，遇到重复时左指针收缩。

```python
def lengthOfLongestSubstring(s):
    seen, l, ans = set(), 0, 0
    for r in range(len(s)):
        while s[r] in seen:
            seen.remove(s[l])
            l += 1
        seen.add(s[r])
        ans = max(ans, r - l + 1)
    return ans
```

### T76. Minimum Window Substring（最小覆盖子串）✅

给定两个字符串 `s` 和 `t`，长度分别是 `m` 和 `n`，返回 s 中的 **最短窗口 子串**，使得该子串包含 `t` 中的每一个字符（**包括重复字符**）。如果没有这样的子串，返回空字符串 `""`。

测试用例保证答案唯一。



**思路**：用两个指针维护窗口，用哈希表记录 t 中字符的需求量。当窗口覆盖所有字符后，尝试收缩左边界。

```python
def minWindow(s, t):
    from collections import Counter
    need = Counter(t)
    missing = len(t)
    l = start = end = 0
    for r, ch in enumerate(s, 1):
        if need[ch] > 0:
            missing -= 1
        need[ch] -= 1 # 不在 t 中的字符，其 need 值会变为负数
        if missing == 0:
            while l < r and need[s[l]] < 0: # 过滤掉左侧无用或冗余的字符
                need[s[l]] += 1
                l += 1
            if end == 0 or r - l < end - start: # 更新全局最短子串
                start, end = l, r
            # 破坏当前窗口的有效性，继续寻找下一个可行解
            need[s[l]] += 1
            missing += 1
            l += 1
    return s[start:end]
```



### 239. Sliding Window Maximum（滑动窗口最大值）

**思路**：单调双端队列，队首始终是当前窗口最大值。窗口滑动时维护队列。

```python
from collections import deque

def maxSlidingWindow(nums, k):
    dq, res = deque(), []
    for i in range(len(nums)):
        while dq and nums[dq[-1]] < nums[i]:
            dq.pop()
        dq.append(i)
        if dq[0] == i - k:
            dq.popleft()
        if i >= k - 1:
            res.append(nums[dq[0]])
    return res
```

### M438. Find All Anagrams in a String（找到字符串中所有字母异位词）✅

给定两个字符串 `s` 和 `p`，找到 `s` 中所有 `p` 的 **异位词** 的子串，返回这些子串的起始索引。不考虑答案输出的顺序。



**思路**：固定长度滑动窗口。用哈希表（或数组）记录窗口内字符计数，与目标串匹配。

```python
def findAnagrams(s, p):
    from collections import Counter
    need = Counter(p)
    missing = len(p)
    res = []
    for r, ch in enumerate(s):
        if need[ch] > 0:
            missing -= 1
        need[ch] -= 1
        if r >= len(p):
            left = s[r - len(p)]
            if need[left] >= 0:
                missing += 1
            need[left] += 1
        if missing == 0:
            res.append(r - len(p) + 1)
    return res
```

---

## 四、子串/子数组

### 53. Maximum Subarray（最大子数组和）

**思路**：Kadane 算法。记录当前子数组和，若为负则重新开始。

```python
def maxSubArray(nums):
    cur = best = nums[0]
    for num in nums[1:]:
        cur = max(num, cur + num)
        best = max(best, cur)
    return best
```

### 152. Maximum Product Subarray（乘积最大子数组）

**思路**：同时维护最大值和最小值（因为负负得正）。

```python
def maxProduct(nums):
    cur_max = cur_min = ans = nums[0]
    for num in nums[1:]:
        candidates = (num, cur_max * num, cur_min * num)
        cur_max, cur_min = max(candidates), min(candidates)
        ans = max(ans, cur_max)
    return ans
```

---

## 五、普通数组

### 31. Next Permutation（下一个排列）

**思路**：从右向左找第一个降序位置 `i`，再找右侧比 `nums[i]` 大的最小数交换，最后反转 `i` 之后的部分。

```python
def nextPermutation(nums):
    i = len(nums) - 2
    while i >= 0 and nums[i] >= nums[i + 1]:
        i -= 1
    if i >= 0:
        j = len(nums) - 1
        while nums[j] <= nums[i]:
            j -= 1
        nums[i], nums[j] = nums[j], nums[i]
    l, r = i + 1, len(nums) - 1
    while l < r:
        nums[l], nums[r] = nums[r], nums[l]
        l += 1
        r -= 1
```

### 41. First Missing Positive（缺失的第一个正数）

**思路**：将每个正数 `x` 放到下标 `x - 1` 位置。遍历找第一个下标与值不对应的位置。

```python
def firstMissingPositive(nums):
    n = len(nums)
    for i in range(n):
        while 1 <= nums[i] <= n and nums[nums[i] - 1] != nums[i]:
            nums[nums[i] - 1], nums[i] = nums[i], nums[nums[i] - 1]
    for i in range(n):
        if nums[i] != i + 1:
            return i + 1
    return n + 1
```

### 189. Rotate Array（轮转数组）

**思路**：三次反转——反转整个数组，反转前 k 个，反转后 n-k 个。

```python
def rotate(nums, k):
    n = len(nums)
    k %= n
    def rev(l, r):
        while l < r:
            nums[l], nums[r] = nums[r], nums[l]
            l += 1; r -= 1
    rev(0, n - 1)
    rev(0, k - 1)
    rev(k, n - 1)
```

### 48. Rotate Image（旋转图像）

**思路**：先按对角线翻转，再每行反转。

```python
def rotate(matrix):
    n = len(matrix)
    for i in range(n):
        for j in range(i, n):
            matrix[i][j], matrix[j][i] = matrix[j][i], matrix[i][j]
    for i in range(n):
        matrix[i].reverse()
```

### M56. Merge Intervals（合并区间）✅

以数组 `intervals` 表示若干个区间的集合，其中单个区间为 `intervals[i] = [starti, endi]` 。请你合并所有重叠的区间，并返回 *一个不重叠的区间数组，该数组需恰好覆盖输入中的所有区间* 。



**思路**：按区间起点排序。遍历合并重叠区间。

```python
def merge(intervals):
    intervals.sort(key=lambda x: x[0])
    res = [intervals[0]]
    for s, e in intervals[1:]:
        if s <= res[-1][1]:
            res[-1][1] = max(res[-1][1], e)
        else:
            res.append([s, e])
    return res
```



### 73. Set Matrix Zeroes（矩阵置零）

**思路**：用第一行和第一列作为标记位。先记录第一行/列是否有零，再标记，最后清零。

```python
def setZeroes(matrix):
    m, n = len(matrix), len(matrix[0])
    row0 = any(matrix[0][j] == 0 for j in range(n))
    col0 = any(matrix[i][0] == 0 for i in range(m))
    for i in range(1, m):
        for j in range(1, n):
            if matrix[i][j] == 0:
                matrix[i][0] = matrix[0][j] = 0
    for i in range(1, m):
        for j in range(1, n):
            if matrix[i][0] == 0 or matrix[0][j] == 0:
                matrix[i][j] = 0
    if row0:
        for j in range(n):
            matrix[0][j] = 0
    if col0:
        for i in range(m):
            matrix[i][0] = 0
```

### 238. Product of Array Except Self（除自身以外数组的乘积）

**思路**：先从左到右算前缀积，再从右到左乘以后缀积。

```python
def productExceptSelf(nums):
    n = len(nums)
    ans = [1] * n
    left = 1
    for i in range(n):
        ans[i] = left
        left *= nums[i]
    right = 1
    for i in range(n - 1, -1, -1):
        ans[i] *= right
        right *= nums[i]
    return ans
```

### 240. Search a 2D Matrix II（搜索二维矩阵 II）

**思路**：从右上角开始，利用行列有序特性，每次排除一行或一列。

```python
def searchMatrix(matrix, target):
    if not matrix:
        return False
    i, j = 0, len(matrix[0]) - 1
    while i < len(matrix) and j >= 0:
        if matrix[i][j] == target:
            return True
        elif matrix[i][j] > target:
            j -= 1
        else:
            i += 1
    return False
```

### 169. Majority Element（多数元素）

**思路**：Boyer-Moore 投票算法。计数，遇到相同数 +1，不同 -1，减到 0 就换候选。

```python
def majorityElement(nums):
    candidate, count = nums[0], 1
    for num in nums[1:]:
        if count == 0:
            candidate, count = num, 1
        elif num == candidate:
            count += 1
        else:
            count -= 1
    return candidate
```

---

## 六、矩阵

### M54. Spiral Matrix（螺旋矩阵）✅

给你一个 `m` 行 `n` 列的矩阵 `matrix` ，请按照 **顺时针螺旋顺序** ，返回矩阵中的所有元素。



**思路**：方向数组+标记

```python
from typing import List

class Solution:
    def spiralOrder(self, matrix: List[List[int]]) -> List[int]:
        if not matrix or not matrix[0]:
            return []

        m, n = len(matrix), len(matrix[0])
        result = []

        # 顺时针方向：右、下、左、上
        directions = [(0, 1), (1, 0), (0, -1), (-1, 0)]
        x, y = 0, 0
        dir_idx = 0

        for _ in range(m * n):
            result.append(matrix[x][y])
            matrix[x][y] = None  # 使用 None 标记已访问，省去 visited 数组

            dx, dy = directions[dir_idx]
            next_x, next_y = x + dx, y + dy

            # 判断是否需要转弯
            if (0 <= next_x < m and 
                0 <= next_y < n and 
                matrix[next_x][next_y] is not None):
                x, y = next_x, next_y
            else:
                dir_idx = (dir_idx + 1) % 4
                dx, dy = directions[dir_idx]
                x, y = x + dx, y + dy

        return result
```



**思路**：模拟上下左右四个边界，按顺时针方向遍历。

```python
def spiralOrder(matrix):
    res = []
    t, b, l, r = 0, len(matrix) - 1, 0, len(matrix[0]) - 1
    while t <= b and l <= r:
        for j in range(l, r + 1):
            res.append(matrix[t][j])
        t += 1
        for i in range(t, b + 1):
            res.append(matrix[i][r])
        r -= 1
        # 在上一步中 t 已经增加了，如果此时 t > b，说明所有的行已经遍历完毕
        if t <= b:
            for j in range(r, l - 1, -1):
                res.append(matrix[b][j])
            b -= 1
        # 在第二步中 r 已经减少了，如果此时 l > r，说明所有的列已经遍历完毕
        if l <= r:
            for i in range(b, t - 1, -1):
                res.append(matrix[i][l])
            l += 1
    return res
```

> 关键细节：为什么需要 if t <= b 和 if l <= r 的判断？
>
> 在非正方形矩阵（例如 3×5 或 5×3 的矩阵）中，遍历到最后可能会退化为单行或单列。
>
> 如果没有这两个条件判断，在已经遍历完所有行/列之后，底部的两个 for 循环仍会继续执行，导致将已经访问过的元素重复添加到结果数组 res 中。加上这两个判断可以确保每一轮边界收缩后，仍然满足遍历的有效性。



### 79. Word Search（单词搜索）

**思路**：DFS + 回溯。遍历每个格子，从该位置出发尝试匹配单词。

```python
def exist(board, word):
    m, n = len(board), len(board[0])
    def dfs(i, j, k):
        if k == len(word):
            return True
        if i < 0 or i >= m or j < 0 or j >= n or board[i][j] != word[k]:
            return False
        board[i][j] = '#'
        for di, dj in ((1,0), (-1,0), (0,1), (0,-1)):
            if dfs(i + di, j + dj, k + 1):
                return True
        board[i][j] = word[k]
        return False
    for i in range(m):
        for j in range(n):
            if dfs(i, j, 0):
                return True
    return False
```

### 200. Number of Islands（岛屿数量）

**思路**：遍历每个格子，遇到 '1' 将其连通的所有 '1' 标记为 '0'（DFS/BFS）。

```python
def numIslands(grid):
    m, n = len(grid), len(grid[0])
    def dfs(i, j):
        if i < 0 or i >= m or j < 0 or j >= n or grid[i][j] != '1':
            return
        grid[i][j] = '0'
        for di, dj in ((1,0), (-1,0), (0,1), (0,-1)):
            dfs(i + di, j + dj)
    ans = 0
    for i in range(m):
        for j in range(n):
            if grid[i][j] == '1':
                ans += 1
                dfs(i, j)
    return ans
```

### 994. Rotting Oranges（腐烂的橘子）

**思路**：多源 BFS。将所有初始腐烂橘子入队，按层扩散。

```python
from collections import deque

def orangesRotting(grid):
    m, n = len(grid), len(grid[0])
    q = deque()
    fresh = 0
    for i in range(m):
        for j in range(n):
            if grid[i][j] == 2:
                q.append((i, j))
            elif grid[i][j] == 1:
                fresh += 1
    if fresh == 0:
        return 0
    minutes = 0
    while q:
        for _ in range(len(q)):
            i, j = q.popleft()
            for di, dj in ((1,0), (-1,0), (0,1), (0,-1)):
                ni, nj = i + di, j + dj
                if 0 <= ni < m and 0 <= nj < n and grid[ni][nj] == 1:
                    grid[ni][nj] = 2
                    fresh -= 1
                    q.append((ni, nj))
        minutes += 1
    return -1 if fresh else minutes - 1
```

---

## 七、链表

### M2. Add Two Numbers（两数相加）✅

给你两个 **非空** 的链表，表示两个非负的整数。它们每位数字都是按照 **逆序** 的方式存储的，并且每个节点只能存储 **一位** 数字。

请你将两个数相加，并以相同形式返回一个表示和的链表。

你可以假设除了数字 0 之外，这两个数都不会以 0 开头。



**思路**：模拟竖式加法。进位逐位相加。

```python
# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, val=0, next=None):
#         self.val = val
#         self.next = next
def addTwoNumbers(l1, l2):
    dummy = cur = ListNode(0)
    carry = 0
    while l1 or l2 or carry:
        s = (l1.val if l1 else 0) + (l2.val if l2 else 0) + carry
        carry = s // 10
        cur.next = ListNode(s % 10)
        cur = cur.next
        if l1: l1 = l1.next
        if l2: l2 = l2.next
    return dummy.next
```



### 19. Remove Nth Node From End of List（删除链表的倒数第 N 个结点）

**思路**：快慢指针。快指针先走 n 步，然后快慢一起走，快到底时慢指针指向待删除节点前一个。

```python
def removeNthFromEnd(head, n):
    dummy = ListNode(0, head)
    fast = slow = dummy
    for _ in range(n):
        fast = fast.next
    while fast.next:
        fast = fast.next
        slow = slow.next
    slow.next = slow.next.next
    return dummy.next
```

### 21. Merge Two Sorted Lists（合并两个有序链表）

**思路**：双指针归并。

```python
def mergeTwoLists(l1, l2):
    dummy = cur = ListNode(0)
    while l1 and l2:
        if l1.val < l2.val:
            cur.next = l1
            l1 = l1.next
        else:
            cur.next = l2
            l2 = l2.next
        cur = cur.next
    cur.next = l1 or l2
    return dummy.next
```

### 23. Merge k Sorted Lists（合并 K 个升序链表）

**思路**：用最小堆（优先队列）每次取出最小节点。

```python
import heapq

def mergeKLists(lists):
    dummy = cur = ListNode(0)
    heap = []
    for i, node in enumerate(lists):
        if node:
            heapq.heappush(heap, (node.val, i, node))
    while heap:
        _, i, node = heapq.heappop(heap)
        cur.next = node
        cur = cur.next
        if node.next:
            heapq.heappush(heap, (node.next.val, i, node.next))
    return dummy.next
```

### 24. Swap Nodes in Pairs（两两交换链表中的节点）

**思路**：递归或迭代。每次交换两个节点，递归处理后续。

```python
def swapPairs(head):
    if not head or not head.next:
        return head
    nxt = head.next
    head.next = swapPairs(nxt.next)
    nxt.next = head
    return nxt
```

### 25. Reverse Nodes in k-Group（K 个一组翻转链表）

**思路**：先检查是否有 k 个节点，然后翻转这 k 个节点，递归处理剩余部分。

```python
def reverseKGroup(head, k):
    node = head
    for _ in range(k):
        if not node:
            return head
        node = node.next
    prev, cur = None, head
    for _ in range(k):
        nxt = cur.next
        cur.next = prev
        prev = cur
        cur = nxt
    head.next = reverseKGroup(cur, k)
    return prev
```

### 138. Copy List with Random Pointer（复制带随机指针的链表）

**思路**：在原链表每个节点后插入复制节点，然后处理 random 指针，最后分离。

```python
def copyRandomList(head):
    if not head:
        return None
    cur = head
    while cur:
        node = Node(cur.val, cur.next, None)
        cur.next = node
        cur = node.next
    cur = head
    while cur:
        if cur.random:
            cur.next.random = cur.random.next
        cur = cur.next.next
    cur = head
    new_head = head.next
    while cur:
        copy = cur.next
        cur.next = copy.next
        cur = cur.next
        if cur:
            copy.next = cur.next
    return new_head
```

### 148. Sort List（排序链表）

**思路**：归并排序。快慢指针找中点，递归排序左右，再合并。

```python
def sortList(head):
    if not head or not head.next:
        return head
    slow = fast = head
    while fast.next and fast.next.next:
        slow = slow.next
        fast = fast.next.next
    mid = slow.next
    slow.next = None
    left = sortList(head)
    right = sortList(mid)
    dummy = cur = ListNode(0)
    while left and right:
        if left.val < right.val:
            cur.next = left
            left = left.next
        else:
            cur.next = right
            right = right.next
        cur = cur.next
    cur.next = left or right
    return dummy.next
```

### M146. LRU Cache（LRU 缓存）✅

请你设计并实现一个满足 [LRU (最近最少使用) 缓存](https://baike.baidu.com/item/LRU) 约束的数据结构。

实现 `LRUCache` 类：

- `LRUCache(int capacity)` 以 **正整数** 作为容量 `capacity` 初始化 LRU 缓存
- `int get(int key)` 如果关键字 `key` 存在于缓存中，则返回关键字的值，否则返回 `-1` 。
- `void put(int key, int value)` 如果关键字 `key` 已经存在，则变更其数据值 `value` ；如果不存在，则向缓存中插入该组 `key-value` 。如果插入操作导致关键字数量超过 `capacity` ，则应该 **逐出** 最久未使用的关键字。

函数 `get` 和 `put` 必须以 `O(1)` 的平均时间复杂度运行。



**思路**：哈希表 + 双向链表。每次访问将节点移到头部，淘汰时移除尾部。

```python
class DLinkedNode:
    def __init__(self, key=0, val=0):
        self.key = key
        self.val = val
        self.prev = None
        self.next = None

class LRUCache:
    def __init__(self, capacity):
        self.cap = capacity
        self.cache = {}
        self.head = DLinkedNode()
        self.tail = DLinkedNode()
        self.head.next = self.tail
        self.tail.prev = self.head

    def _remove(self, node):
        node.prev.next = node.next
        node.next.prev = node.prev

    def _add_to_head(self, node):
        node.next = self.head.next
        node.prev = self.head
        self.head.next.prev = node
        self.head.next = node

    def get(self, key):
        if key not in self.cache:
            return -1
        node = self.cache[key]
        self._remove(node)
        self._add_to_head(node)
        return node.val

    def put(self, key, val):
        if key in self.cache:
            node = self.cache[key]
            node.val = val
            self._remove(node)
            self._add_to_head(node)
        else:
            node = DLinkedNode(key, val)
            self.cache[key] = node
            self._add_to_head(node)
            if len(self.cache) > self.cap:
                rm = self.tail.prev
                self._remove(rm)
                del self.cache[rm.key]
```



### 206. Reverse Linked List（反转链表）

**思路**：迭代，用 prev 和 cur 两个指针逐步反转。

```python
def reverseList(head):
    prev = None
    cur = head
    while cur:
        nxt = cur.next
        cur.next = prev
        prev = cur
        cur = nxt
    return prev
```

### 141. Linked List Cycle（环形链表）

**思路**：快慢指针，相遇即有环。

```python
def hasCycle(head):
    slow = fast = head
    while fast and fast.next:
        slow = slow.next
        fast = fast.next.next
        if slow == fast:
            return True
    return False
```

### 142. Linked List Cycle II（环形链表 II）

**思路**：快慢指针相遇后，一个指针从头开始，一个从相遇点，再次相遇即为入环点。

```python
def detectCycle(head):
    slow = fast = head
    while fast and fast.next:
        slow = slow.next
        fast = fast.next.next
        if slow == fast:
            slow = head
            while slow != fast:
                slow = slow.next
                fast = fast.next
            return slow
    return None
```

### 160. Intersection of Two Linked Lists（相交链表）

**思路**：两指针分别从 a、b 开始，走完自己链表后走对方链表，相遇点即为交点。

```python
def getIntersectionNode(headA, headB):
    a, b = headA, headB
    while a != b:
        a = a.next if a else headB
        b = b.next if b else headA
    return a
```

### 234. Palindrome Linked List（回文链表）

**思路**：快慢指针找中点，反转后半部分，比较两半。

```python
def isPalindrome(head):
    slow = fast = head
    while fast and fast.next:
        slow = slow.next
        fast = fast.next.next
    prev = None
    while slow:
        nxt = slow.next
        slow.next = prev
        prev = slow
        slow = nxt
    while prev:
        if head.val != prev.val:
            return False
        head = head.next
        prev = prev.next
    return True
```

---

## 八、二叉树

### 94. Binary Tree Inorder Traversal（二叉树的中序遍历）

**思路**：递归（左-根-右）或栈模拟。

```python
def inorderTraversal(root):
    res = []
    def inorder(node):
        if not node: return
        inorder(node.left)
        res.append(node.val)
        inorder(node.right)
    inorder(root)
    return res
```

### 101. Symmetric Tree（对称二叉树）

**思路**：递归比较左子树的左孩子与右子树的右孩子，左子树的右孩子与右子树的左孩子。

```python
def isSymmetric(root):
    def check(l, r):
        if not l and not r: return True
        if not l or not r or l.val != r.val: return False
        return check(l.left, r.right) and check(l.right, r.left)
    return check(root.left, root.right) if root else True
```

### 102. Binary Tree Level Order Traversal（二叉树的层序遍历）

**思路**：BFS 用队列，每层一次性处理全部节点。

```python
from collections import deque

def levelOrder(root):
    if not root: return []
    res, q = [], deque([root])
    while q:
        level = []
        for _ in range(len(q)):
            node = q.popleft()
            level.append(node.val)
            if node.left: q.append(node.left)
            if node.right: q.append(node.right)
        res.append(level)
    return res
```

### 104. Maximum Depth of Binary Tree（二叉树的最大深度）

**思路**：递归求左右子树最大深度 + 1。

```python
def maxDepth(root):
    if not root: return 0
    return 1 + max(maxDepth(root.left), maxDepth(root.right))
```

### 105. Construct Binary Tree from Preorder and Inorder Traversal（从前序与中序遍历序列构造二叉树）

**思路**：前序第一个为根，在中序中找到根的位置，左右部分递归构建。

```python
def buildTree(preorder, inorder):
    idx_map = {val: i for i, val in enumerate(inorder)}
    def build(pl, pr, il, ir):
        if pl > pr: return None
        root = TreeNode(preorder[pl])
        i = idx_map[root.val]
        left_len = i - il
        root.left = build(pl + 1, pl + left_len, il, i - 1)
        root.right = build(pl + left_len + 1, pr, i + 1, ir)
        return root
    return build(0, len(preorder) - 1, 0, len(inorder) - 1)
```

### 108. Convert Sorted Array to Binary Search Tree（将有序数组转换为二叉搜索树）

**思路**：取中间元素为根，左右递归构建。

```python
def sortedArrayToBST(nums):
    def build(l, r):
        if l > r: return None
        mid = (l + r) // 2
        root = TreeNode(nums[mid])
        root.left = build(l, mid - 1)
        root.right = build(mid + 1, r)
        return root
    return build(0, len(nums) - 1)
```

### M114. Flatten Binary Tree to Linked List（二叉树展开为链表）✅

给你二叉树的根结点 `root` ，请你将它展开为一个单链表：

- 展开后的单链表应该同样使用 `TreeNode` ，其中 `right` 子指针指向链表中下一个结点，而左子指针始终为 `null` 。

- 展开后的单链表应该与二叉树 [**先序遍历**](https://baike.baidu.com/item/先序遍历/6442839?fr=aladdin) 顺序相同。

  

**思路**：“寻找前驱节点”方法。先序遍历，每次将左子树移到右子树位置，再将原右子树接到左子树最右边。

```python
def flatten(root):
    cur = root
    while cur:
        # 如果当前节点有左子树，才需要进行旋转和拼接
        if cur.left:
            # 1. 寻找左子树的最右节点（先序遍历中，左子树里最后被访问的节点）
            pre = cur.left
            while pre.right:
                pre = pre.right
            
            # 2. 将原来的右子树接到这个最右节点的右边
            pre.right = cur.right
            
            # 3. 将左子树整体移动到右边
            cur.right = cur.left
            
            # 4. 将左子树清空
            cur.left = None
            
        # 无论是否有左子树，cur 都向右移动一步
        cur = cur.right
```



### 124. Binary Tree Maximum Path Sum（二叉树中的最大路径和）

**思路**：后序遍历。对每个节点，计算经过该节点的最大路径和，同时返回以该节点为端点的最大单边和。

```python
def maxPathSum(root):
    ans = -float('inf')
    def dfs(node):
        nonlocal ans
        if not node: return 0
        left = max(dfs(node.left), 0)
        right = max(dfs(node.right), 0)
        ans = max(ans, left + right + node.val)
        return node.val + max(left, right)
    dfs(root)
    return ans
```

### 199. Binary Tree Right Side View（二叉树的右视图）

**思路**：层序遍历，每层取最右节点。

```python
from collections import deque

def rightSideView(root):
    if not root: return []
    res, q = [], deque([root])
    while q:
        res.append(q[-1].val)
        for _ in range(len(q)):
            node = q.popleft()
            if node.left: q.append(node.left)
            if node.right: q.append(node.right)
    return res
```

### 226. Invert Binary Tree（翻转二叉树）

**思路**：递归交换左右子树。

```python
def invertTree(root):
    if not root: return None
    root.left, root.right = invertTree(root.right), invertTree(root.left)
    return root
```

### 236. Lowest Common Ancestor of a Binary Tree（二叉树的最近公共祖先）

**思路**：递归查找。若当前节点是 p 或 q 则返回；左右子树都找到则当前为 LCA。

```python
def lowestCommonAncestor(root, p, q):
    if not root or root == p or root == q:
        return root
    left = lowestCommonAncestor(root.left, p, q)
    right = lowestCommonAncestor(root.right, p, q)
    if left and right:
        return root
    return left or right
```

### 437. Path Sum III（路径总和 III）

**思路**：前缀和 + DFS。用哈希表记录从根到当前节点的路径和出现次数。

```python
def pathSum(root, targetSum):
    prefix = {0: 1}
    ans = 0
    def dfs(node, cur):
        nonlocal ans
        if not node: return
        cur += node.val
        ans += prefix.get(cur - targetSum, 0)
        prefix[cur] = prefix.get(cur, 0) + 1
        dfs(node.left, cur)
        dfs(node.right, cur)
        prefix[cur] -= 1
    dfs(root, 0)
    return ans
```

### 543. Diameter of Binary Tree（二叉树的直径）

**思路**：后序遍历，对每个节点求左右子树深度和，取最大值。

```python
def diameterOfBinaryTree(root):
    ans = 0
    def depth(node):
        nonlocal ans
        if not node: return 0
        left = depth(node.left)
        right = depth(node.right)
        ans = max(ans, left + right)
        return 1 + max(left, right)
    depth(root)
    return ans
```

---

## 九、二叉搜索树

### 98. Validate Binary Search Tree（验证二叉搜索树）

**思路**：中序遍历递增。或递归传入 min/max 边界。

```python
def isValidBST(root):
    def valid(node, lo, hi):
        if not node: return True
        if not (lo < node.val < hi): return False
        return valid(node.left, lo, node.val) and valid(node.right, node.val, hi)
    return valid(root, -float('inf'), float('inf'))
```

### 230. Kth Smallest Element in a BST（二叉搜索树中第 K 小的元素）

**思路**：中序遍历（BST 的中序为升序），遍历到第 k 个即结果。

```python
def kthSmallest(root, k):
    stack = []
    while stack or root:
        while root:
            stack.append(root)
            root = root.left
        root = stack.pop()
        k -= 1
        if k == 0: return root.val
        root = root.right
```

---

## 十、二分查找

### 4. Median of Two Sorted Arrays（寻找两个正序数组的中位数）

**思路**：二分较短的数组，保证两个数组的前半部分元素个数相等。

```python
def findMedianSortedArrays(nums1, nums2):
    if len(nums1) > len(nums2):
        nums1, nums2 = nums2, nums1
    m, n = len(nums1), len(nums2)
    lo, hi = 0, m
    while lo <= hi:
        i = (lo + hi) // 2
        j = (m + n + 1) // 2 - i
        left1 = nums1[i - 1] if i > 0 else -float('inf')
        right1 = nums1[i] if i < m else float('inf')
        left2 = nums2[j - 1] if j > 0 else -float('inf')
        right2 = nums2[j] if j < n else float('inf')
        if left1 <= right2 and left2 <= right1:
            if (m + n) % 2 == 0:
                return (max(left1, left2) + min(right1, right2)) / 2
            else:
                return max(left1, left2)
        elif left1 > right2:
            hi = i - 1
        else:
            lo = i + 1
```

### M33. Search in Rotated Sorted Array（搜索旋转排序数组）✅

整数数组 `nums` 按升序排列，数组中的值 **互不相同** 。

在传递给函数之前，`nums` 在预先未知的某个下标 `k`（`0 <= k < nums.length`）上进行了 **向左旋转**，使数组变为 `[nums[k], nums[k+1], ..., nums[n-1], nums[0], nums[1], ..., nums[k-1]]`（下标 **从 0 开始** 计数）。例如， `[0,1,2,4,5,6,7]` 下标 `3` 上向左旋转后可能变为 `[4,5,6,7,0,1,2]` 。

给你 **旋转后** 的数组 `nums` 和一个整数 `target` ，如果 `nums` 中存在这个目标值 `target` ，则返回它的下标，否则返回 `-1` 。

你必须设计一个时间复杂度为 `O(log n)` 的算法解决此问题。



**思路**：二分。判断 mid 落在哪一段有序区间，再决定搜索范围。

```python
def search(nums, target):
    l, r = 0, len(nums) - 1
    while l <= r:
        mid = (l + r) // 2
        if nums[mid] == target: return mid
        if nums[l] <= nums[mid]:
            if nums[l] <= target < nums[mid]:
                r = mid - 1
            else:
                l = mid + 1
        else:
            if nums[mid] < target <= nums[r]:
                l = mid + 1
            else:
                r = mid - 1
    return -1
```

> 算法的严谨性在于：
>
> 1. **分类讨论不重不漏**：利用 nums[l] <= nums[mid] 将空间划分为两种对立的情况。
> 2. **条件判定充分必要**：在有序的一侧，利用单调性通过双侧边界直接确认 target 的存在性；若不满足，则通过排他性进入另一侧。
> 3. **收缩行为必定收敛**：由于跳过了已验证的 mid 点，区间长度在每一步都严格减小，直至 l > r 循环自然终止。



### 34. Find First and Last Position of Element in Sorted Array（在排序数组中查找元素的第一个和最后一个位置）

**思路**：二分查找左边界和右边界。

```python
def searchRange(nums, target):
    def bisect(lo, hi, left_bound):
        while lo <= hi:
            mid = (lo + hi) // 2
            if nums[mid] > target or (left_bound and nums[mid] == target):
                hi = mid - 1
            else:
                lo = mid + 1
        return lo
    n = len(nums)
    l = bisect(0, n - 1, True)
    if l == n or nums[l] != target: return [-1, -1]
    r = bisect(0, n - 1, False) - 1
    return [l, r]
```

### 35. Search Insert Position（搜索插入位置）

**思路**：标准二分查找。

```python
def searchInsert(nums, target):
    l, r = 0, len(nums)
    while l < r:
        mid = (l + r) // 2
        if nums[mid] >= target:
            r = mid
        else:
            l = mid + 1
    return l
```

### 74. Search a 2D Matrix（搜索二维矩阵）

**思路**：将二维矩阵拉直成一维数组进行二分。

```python
def searchMatrix(matrix, target):
    m, n = len(matrix), len(matrix[0])
    l, r = 0, m * n - 1
    while l <= r:
        mid = (l + r) // 2
        val = matrix[mid // n][mid % n]
        if val == target: return True
        if val < target: l = mid + 1
        else: r = mid - 1
    return False
```

### 153. Find Minimum in Rotated Sorted Array（寻找旋转排序数组中的最小值）

**思路**：二分。如果 `nums[mid] > nums[r]`，最小值在右边；否则在左边（含 mid）。

```python
def findMin(nums):
    l, r = 0, len(nums) - 1
    while l < r:
        mid = (l + r) // 2
        if nums[mid] > nums[r]:
            l = mid + 1
        else:
            r = mid
    return nums[l]
```

---

## 十一、栈

### 20. Valid Parentheses（有效的括号）

**思路**：用栈匹配。遇到左括号入栈，遇到右括号检查栈顶是否匹配。

```python
def isValid(s):
    pairs = {')': '(', ']': '[', '}': '{'}
    stack = []
    for ch in s:
        if ch in pairs:
            if not stack or stack[-1] != pairs[ch]:
                return False
            stack.pop()
        else:
            stack.append(ch)
    return not stack
```

### 32. Longest Valid Parentheses（最长有效括号）

**思路**：用栈存储下标。初始化栈为 `[-1]`。遇到 '(' 入栈，遇到 ')' 出栈，若栈空则压入当前下标。

```python
def longestValidParentheses(s):
    stack, ans = [-1], 0
    for i, ch in enumerate(s):
        if ch == '(':
            stack.append(i)
        else:
            stack.pop()
            if not stack:
                stack.append(i)
            else:
                ans = max(ans, i - stack[-1])
    return ans
```

### 84. Largest Rectangle in Histogram（柱状图中最大的矩形）

**思路**：单调递增栈。遇到更矮的柱子时，弹出栈顶并计算以该高度为高的矩形面积。

```python
def largestRectangleArea(heights):
    stack, ans = [], 0
    heights = [0] + heights + [0]
    for i in range(len(heights)):
        while stack and heights[stack[-1]] > heights[i]:
            h = heights[stack.pop()]
            w = i - stack[-1] - 1
            ans = max(ans, h * w)
        stack.append(i)
    return ans
```

### M155. Min Stack（最小栈）✅

设计一个支持 `push` ，`pop` ，`top` 操作，并能在常数时间内检索到最小元素的栈。

实现 `MinStack` 类:

- `MinStack()` 初始化堆栈对象。
- `void push(int val)` 将元素val推入堆栈。
- `void pop()` 删除堆栈顶部的元素。
- `int top()` 获取堆栈顶部的元素。
- `int getMin()` 获取堆栈中的最小元素。



**思路**：辅助栈存最小值。

```python
class MinStack:
    def __init__(self):
        self.stack = []
        self.min_stack = [float('inf')]

    def push(self, val):
        self.stack.append(val)
        self.min_stack.append(min(val, self.min_stack[-1]))

    def pop(self):
        self.stack.pop()
        self.min_stack.pop()

    def top(self):
        return self.stack[-1]

    def getMin(self):
        return self.min_stack[-1]
```



### 394. Decode String（字符串解码）

**思路**：用栈存储数字和当前字符串。遇到 '[' 入栈，遇到 ']' 出栈并重复拼接。

```python
def decodeString(s):
    stack = []
    cur_str = ''
    cur_num = 0
    for ch in s:
        if ch.isdigit():
            cur_num = cur_num * 10 + int(ch)
        elif ch == '[':
            stack.append((cur_str, cur_num))
            cur_str, cur_num = '', 0
        elif ch == ']':
            prev_str, num = stack.pop()
            cur_str = prev_str + cur_str * num
        else:
            cur_str += ch
    return cur_str
```

### 739. Daily Temperatures（每日温度）

**思路**：单调递减栈（存下标）。遇到更高的温度则出栈并计算结果。

```python
def dailyTemperatures(temperatures):
    n = len(temperatures)
    ans = [0] * n
    stack = []
    for i in range(n):
        while stack and temperatures[i] > temperatures[stack[-1]]:
            idx = stack.pop()
            ans[idx] = i - idx
        stack.append(i)
    return ans
```

---

## 十二、堆

### 215. Kth Largest Element in an Array（数组中的第 K 个最大元素）

**思路**：快速选择（Quick Select）或最小堆。

```python
def findKthLargest(nums, k):
    import heapq
    heap = nums[:k]
    heapq.heapify(heap)
    for num in nums[k:]:
        if num > heap[0]:
            heapq.heapreplace(heap, num)
    return heap[0]
```

### 295. Find Median from Data Stream（数据流的中位数）

**思路**：一个大根堆存较小一半，一个小根堆存较大一半。保持两堆平衡。

```python
import heapq

class MedianFinder:
    def __init__(self):
        self.small = []   # 大根堆（存负数）
        self.large = []   # 小根堆

    def addNum(self, num):
        heapq.heappush(self.small, -num)
        heapq.heappush(self.large, -heapq.heappop(self.small))
        if len(self.large) > len(self.small):
            heapq.heappush(self.small, -heapq.heappop(self.large))

    def findMedian(self):
        if len(self.small) > len(self.large):
            return -self.small[0]
        return (-self.small[0] + self.large[0]) / 2
```

### 347. Top K Frequent Elements（前 K 个高频元素）

**思路**：先用哈希表统计频率，再用最小堆维护前 k 高频。

```python
def topKFrequent(nums, k):
    from collections import Counter
    import heapq
    freq = Counter(nums)
    heap = []
    for num, f in freq.items():
        heapq.heappush(heap, (f, num))
        if len(heap) > k:
            heapq.heappop(heap)
    return [num for _, num in heap]
```

---

## 十三、贪心

### M45. Jump Game II（跳跃游戏 II）✅

给定一个长度为 `n` 的 **0 索引**整数数组 `nums`。初始位置在下标 0。

每个元素 `nums[i]` 表示从索引 `i` 向后跳转的最大长度。换句话说，如果你在索引 `i` 处，你可以跳转到任意 `(i + j)` 处：

- `0 <= j <= nums[i]` 且
- `i + j < n`

返回到达 `n - 1` 的最小跳跃次数。测试用例保证可以到达 `n - 1`。



**思路**：维护当前能跳到的最远位置和边界，每次到达边界步数 +1。

```python
def jump(nums):
    n = len(nums)
    if n == 1: return 0  # 如果数组长度为 1，无需跳跃，直接返回 0
    
    steps = cur_end = cur_far = 0
    
    # 注意：循环范围是 n - 1，因为不需要遍历最后一个元素。
    # 当我们到达 n - 2 时，根据题目保证可达，此时计算出的下一次边界必然能够覆盖 n - 1。
    for i in range(n - 1):
        # 1. 维护在当前步骤范围内，能起跳到的最远位置
        cur_far = max(cur_far, i + nums[i])
        
        # 2. 当遍历到当前这一步的边界时
        if i == cur_end:
            steps += 1         # 必须再跳一次才能继续往前
            cur_end = cur_far  # 更新下一次跳跃的边界
            
            # 提前结束优化：如果当前能到达的最远位置已经覆盖了最后一个元素，可直接结束
            if cur_end >= n - 1: 
                break
                
    return steps
```



### 55. Jump Game（跳跃游戏）

**思路**：维护能到达的最远位置。若当前 i 超过了最远位置则失败。

```python
def canJump(nums):
    farthest = 0
    for i in range(len(nums)):
        if i > farthest: return False
        farthest = max(farthest, i + nums[i])
    return True
```

### 121. Best Time to Buy and Sell Stock（买卖股票的最佳时机）

**思路**：遍历，记录历史最低价，每天计算利润。

```python
def maxProfit(prices):
    min_price = float('inf')
    ans = 0
    for p in prices:
        if p < min_price:
            min_price = p
        elif p - min_price > ans:
            ans = p - min_price
    return ans
```

### 763. Partition Labels（划分字母区间）

**思路**：先记录每个字母最后出现的位置。然后遍历，维护当前段右边界，到达边界时切分。

```python
def partitionLabels(s):
    last = {c: i for i, c in enumerate(s)}
    ans = []
    start = end = 0
    for i, c in enumerate(s):
        end = max(end, last[c])
        if i == end:
            ans.append(end - start + 1)
            start = i + 1
    return ans
```

---

## 十四、回溯

### 17. Letter Combinations of a Phone Number（电话号码的字母组合）

**思路**：回溯。逐位递归生成所有组合。

```python
def letterCombinations(digits):
    if not digits: return []
    mapping = {'2': 'abc', '3': 'def', '4': 'ghi', '5': 'jkl',
               '6': 'mno', '7': 'pqrs', '8': 'tuv', '9': 'wxyz'}
    res = []
    def backtrack(i, cur):
        if i == len(digits):
            res.append(cur)
            return
        for ch in mapping[digits[i]]:
            backtrack(i + 1, cur + ch)
    backtrack(0, '')
    return res
```

### 22. Generate Parentheses（括号生成）

**思路**：回溯。只当左括号数 < n 时加左括号，右括号数 < 左括号数时加右括号。

```python
def generateParenthesis(n):
    res = []
    def backtrack(l, r, cur):
        if l == n and r == n:
            res.append(cur)
            return
        if l < n:
            backtrack(l + 1, r, cur + '(')
        if r < l:
            backtrack(l, r + 1, cur + ')')
    backtrack(0, 0, '')
    return res
```

### 39. Combination Sum（组合总和）

**思路**：回溯。候选可重复使用，每次从当前下标开始尝试。

```python
def combinationSum(candidates, target):
    res = []
    def backtrack(start, cur, total):
        if total == target:
            res.append(cur[:])
            return
        if total > target: return
        for i in range(start, len(candidates)):
            cur.append(candidates[i])
            backtrack(i, cur, total + candidates[i])
            cur.pop()
    backtrack(0, [], 0)
    return res
```

### 46. Permutations（全排列）

**思路**：回溯。用 visited 标记已选元素。

```python
def permute(nums):
    res = []
    def backtrack(cur, used):
        if len(cur) == len(nums):
            res.append(cur[:])
            return
        for i in range(len(nums)):
            if not used[i]:
                used[i] = True
                cur.append(nums[i])
                backtrack(cur, used)
                cur.pop()
                used[i] = False
    backtrack([], [False] * len(nums))
    return res
```

### 78. Subsets（子集）

**思路**：回溯。每个元素选或不选。

```python
def subsets(nums):
    res = []
    def backtrack(start, cur):
        res.append(cur[:])
        for i in range(start, len(nums)):
            cur.append(nums[i])
            backtrack(i + 1, cur)
            cur.pop()
    backtrack(0, [])
    return res
```

### 51. N-Queens（N 皇后）

**思路**：回溯。用列、主对角线、副对角线的集合判断是否可放置。

```python
def solveNQueens(n):
    res = []
    cols, diag1, diag2 = set(), set(), set()
    board = [['.'] * n for _ in range(n)]
    def backtrack(row):
        if row == n:
            res.append([''.join(r) for r in board])
            return
        for col in range(n):
            if col in cols or (row - col) in diag1 or (row + col) in diag2:
                continue
            board[row][col] = 'Q'
            cols.add(col); diag1.add(row - col); diag2.add(row + col)
            backtrack(row + 1)
            board[row][col] = '.'
            cols.remove(col); diag1.remove(row - col); diag2.remove(row + col)
    backtrack(0)
    return res
```

### 131. Palindrome Partitioning（分割回文串）

**思路**：回溯。在每步检查当前前缀是否为回文。

```python
def partition(s):
    res = []
    def backtrack(start, cur):
        if start == len(s):
            res.append(cur[:])
            return
        for end in range(start + 1, len(s) + 1):
            if s[start:end] == s[start:end][::-1]:
                cur.append(s[start:end])
                backtrack(end, cur)
                cur.pop()
    backtrack(0, [])
    return res
```

---

## 十五、动态规划

### 5. Longest Palindromic Substring（最长回文子串）

**思路**：中心扩展。每个位置（及两位置之间）向两边扩展，找最长回文。

```python
def longestPalindrome(s):
    def expand(l, r):
        while l >= 0 and r < len(s) and s[l] == s[r]:
            l -= 1; r += 1
        return s[l + 1:r]
    res = ''
    for i in range(len(s)):
        s1 = expand(i, i)
        s2 = expand(i, i + 1)
        res = max(res, s1, s2, key=len)
    return res
```

### 62. Unique Paths（不同路径）

**思路**：`dp[i][j] = dp[i-1][j] + dp[i][j-1]`，空间可优化为一维。

```python
def uniquePaths(m, n):
    row = [1] * n
    for _ in range(1, m):
        for j in range(1, n):
            row[j] += row[j - 1]
    return row[-1]
```

### 64. Minimum Path Sum（最小路径和）

**思路**：`dp[i][j] = grid[i][j] + min(dp[i-1][j], dp[i][j-1])`。

```python
def minPathSum(grid):
    m, n = len(grid), len(grid[0])
    for i in range(1, n):
        grid[0][i] += grid[0][i - 1]
    for i in range(1, m):
        grid[i][0] += grid[i - 1][0]
    for i in range(1, m):
        for j in range(1, n):
            grid[i][j] += min(grid[i - 1][j], grid[i][j - 1])
    return grid[-1][-1]
```

### E70. Climbing Stairs（爬楼梯）✅

假设你正在爬楼梯。需要 `n` 阶你才能到达楼顶。

每次你可以爬 `1` 或 `2` 个台阶。你有多少种不同的方法可以爬到楼顶呢？



**思路**：`dp[i] = dp[i-1] + dp[i-2]`，即斐波那契数列。

```python
def climbStairs(self, n: int) -> int:
    if n == 1:
        return 1

    dp = [0]*(n+1)
    dp[1], dp[2]= 1, 2
    for i in range(3, n+1):
        dp[i] = dp[i-1] + dp[i-2]

    return dp[n]
```

> 每次只需要“前两个数”，其实不需要开辟一个完整的数组 dp，只需要用**两个变量**把最近的两个结果存起来，像窗口一样往后滑动即可。用 `a` 代替 `dp[i-2]`，用 `b` 代替 `dp[i-1]`。
>
> ```python
> def climbStairs(self, n: int) -> int:
>     if n == 1:
>         return 1
> 
>     # a 相当于 dp[1]，b 相当于 dp[2]
>     a = 1
>     b = 2
> 
>     # 从 3 开始往后算
>     for i in range(3, n + 1):
>         c = a + b  # c 相当于当前的 dp[i]
>         a = b  # 把 b 的值给 a，相当于把窗口向右移动一步
>         b = c  # 把 c 的值给 b，继续向右移动一步
> 
>     return b
> ```
>
> 在 Python 中，可以利用“元组赋值”的特性，省去中间变量 `c`：
>
> ```python
> a, b = b, a + b
> ```
>
> 这一行代码相当于同时完成了以下两步：
>
> 1.  计算新的值 `a + b`（即上一步里的 `c`）。
> 2.  把旧的 `b` 赋给 `a`，把新算出来的 `a + b` 赋给 `b`。
>



初始化和循环边界的微调，演变出简化版代码

```python
def climbStairs(n):
    a = b = 1 # # 相当于从 dp[0]=1, dp[1]=1 开始
    for _ in range(n - 1):
        a, b = b, a + b
    return b
```





### 72. Edit Distance（编辑距离）

**思路**：`dp[i][j]` 表示 word1[:i] 到 word2[:j] 的最小编辑距离。

```python
def minDistance(word1, word2):
    m, n = len(word1), len(word2)
    dp = [[0] * (n + 1) for _ in range(m + 1)]
    for i in range(m + 1): dp[i][0] = i
    for j in range(n + 1): dp[0][j] = j
    for i in range(1, m + 1):
        for j in range(1, n + 1):
            if word1[i - 1] == word2[j - 1]:
                dp[i][j] = dp[i - 1][j - 1]
            else:
                dp[i][j] = 1 + min(dp[i - 1][j], dp[i][j - 1], dp[i - 1][j - 1])
    return dp[m][n]
```

### E118. Pascal's Triangle（杨辉三角）✅

给定一个非负整数 *`numRows`，*生成「杨辉三角」的前 *`numRows`* 行。

在**「杨辉三角」**中，每个数是它左上方和右上方的数的和。



**思路**：每行首尾为 1，中间为上一行相邻两数之和。

```python
def generate(numRows):
    res = [[1]]
    for _ in range(1, numRows):
        prev = res[-1]
        cur = [1] + [prev[i] + prev[i + 1] for i in range(len(prev) - 1)] + [1]
        res.append(cur)
    return res
```



### 139. Word Break（单词拆分）

**思路**：`dp[i]` 表示 s[:i] 是否能被拆分。枚举拆分点 j，若 `dp[j]` 且 `s[j:i] in wordDict` 则 `dp[i] = True`。

```python
def wordBreak(s, wordDict):
    words = set(wordDict)
    dp = [True] + [False] * len(s)
    for i in range(1, len(s) + 1):
        for j in range(i):
            if dp[j] and s[j:i] in words:
                dp[i] = True
                break
    return dp[-1]
```

### M198. House Robber（打家劫舍）✅

你是一个专业的小偷，计划偷窃沿街的房屋。每间房内都藏有一定的现金，影响你偷窃的唯一制约因素就是相邻的房屋装有相互连通的防盗系统，**如果两间相邻的房屋在同一晚上被小偷闯入，系统会自动报警**。

给定一个代表每个房屋存放金额的非负整数数组，计算你 **不触动警报装置的情况下** ，一夜之内能够偷窃到的最高金额。



**思路**：`dp[i] = max(dp[i-1], dp[i-2] + nums[i])`。

```python
def rob(nums):
    prev = cur = 0
    for num in nums:
        prev, cur = cur, max(cur, prev + num)
    return cur
```



### M279. Perfect Squares（完全平方数）

**思路**：`dp[i] = min(dp[i], dp[i - j*j] + 1)`。

```python
def numSquares(n):
    dp = [float('inf')] * (n + 1)
    dp[0] = 0
    for i in range(1, n + 1):
        j = 1
        while j * j <= i:
            dp[i] = min(dp[i], dp[i - j * j] + 1)
            j += 1
    return dp[n]
```

### 300. Longest Increasing Subsequence（最长递增子序列）

**思路**：`dp[i] = max(dp[i], dp[j] + 1)` 对于 `nums[j] < nums[i]`。优化：贪心 + 二分。

```python
def lengthOfLIS(nums):
    tails = []
    for num in nums:
        l, r = 0, len(tails)
        while l < r:
            mid = (l + r) // 2
            if tails[mid] < num:
                l = mid + 1
            else:
                r = mid
        if l == len(tails):
            tails.append(num)
        else:
            tails[l] = num
    return len(tails)
```

### 322. Coin Change（零钱兑换）

**思路**：`dp[i] = min(dp[i], dp[i - coin] + 1)`。

```python
def coinChange(coins, amount):
    dp = [float('inf')] * (amount + 1)
    dp[0] = 0
    for i in range(1, amount + 1):
        for coin in coins:
            if coin <= i:
                dp[i] = min(dp[i], dp[i - coin] + 1)
    return dp[amount] if dp[amount] != float('inf') else -1
```

### 416. Partition Equal Subset Sum（分割等和子集）

**思路**：0-1 背包问题。`dp[i]` 表示是否能够凑出和为 i 的子集。

```python
def canPartition(nums):
    total = sum(nums)
    if total % 2: return False
    target = total // 2
    dp = [False] * (target + 1)
    dp[0] = True
    for num in nums:
        for i in range(target, num - 1, -1):
            dp[i] = dp[i] or dp[i - num]
    return dp[target]
```

### 1143. Longest Common Subsequence（最长公共子序列）

**思路**：`dp[i][j]` 表示 text1[:i] 和 text2[:j] 的最长公共子序列长度。

```python
def longestCommonSubsequence(text1, text2):
    m, n = len(text1), len(text2)
    dp = [[0] * (n + 1) for _ in range(m + 1)]
    for i in range(1, m + 1):
        for j in range(1, n + 1):
            if text1[i - 1] == text2[j - 1]:
                dp[i][j] = dp[i - 1][j - 1] + 1
            else:
                dp[i][j] = max(dp[i - 1][j], dp[i][j - 1])
    return dp[m][n]
```

---

## 十六、图论

### 207. Course Schedule（课程表）

**思路**：拓扑排序（Kahn 算法）。统计入度，入度为 0 的节点入队。

```python
def canFinish(numCourses, prerequisites):
    from collections import deque
    indeg = [0] * numCourses
    graph = [[] for _ in range(numCourses)]
    for u, v in prerequisites:
        graph[v].append(u)
        indeg[u] += 1
    q = deque([i for i in range(numCourses) if indeg[i] == 0])
    cnt = 0
    while q:
        node = q.popleft()
        cnt += 1
        for nei in graph[node]:
            indeg[nei] -= 1
            if indeg[nei] == 0:
                q.append(nei)
    return cnt == numCourses
```

---

## 十七、设计/其他

### 208. Implement Trie (Prefix Tree)（实现 Trie 前缀树）

**思路**：每个节点包含一个长度为 26 的子节点数组和一个结束标志。

```python
class TrieNode:
    def __init__(self):
        self.children = {}
        self.is_end = False

class Trie:
    def __init__(self):
        self.root = TrieNode()

    def insert(self, word):
        node = self.root
        for ch in word:
            if ch not in node.children:
                node.children[ch] = TrieNode()
            node = node.children[ch]
        node.is_end = True

    def search(self, word):
        node = self.root
        for ch in word:
            if ch not in node.children:
                return False
            node = node.children[ch]
        return node.is_end

    def startsWith(self, prefix):
        node = self.root
        for ch in prefix:
            if ch not in node.children:
                return False
            node = node.children[ch]
        return True
```

---

## 总结表

| # | 题目 | 难度 | 分类 | 核心思路 |
|---|---|---|---|---|
| 1 | Two Sum | Easy | 哈希表 | 哈希表记录下标 |
| 2 | Add Two Numbers | Medium | 链表 | 模拟竖式加法 |
| 3 | Longest Substring Without Repeating Characters | Medium | 滑动窗口 | 哈希集+双指针 |
| 4 | Median of Two Sorted Arrays | Hard | 二分查找 | 二分较短的数组 |
| 5 | Longest Palindromic Substring | Medium | DP | 中心扩展 |
| 11 | Container With Most Water | Medium | 双指针 | 移动较矮的边 |
| 15 | 3Sum | Medium | 双指针 | 排序+双指针 |
| 17 | Letter Combinations of a Phone Number | Medium | 回溯 | 递归生成组合 |
| 19 | Remove Nth Node From End of List | Medium | 链表 | 快慢指针 |
| 20 | Valid Parentheses | Easy | 栈 | 栈匹配 |
| 21 | Merge Two Sorted Lists | Easy | 链表 | 双指针归并 |
| 22 | Generate Parentheses | Medium | 回溯 | 左右括号计数回溯 |
| 23 | Merge k Sorted Lists | Hard | 链表/堆 | 最小堆 |
| 24 | Swap Nodes in Pairs | Medium | 链表 | 递归交换 |
| 25 | Reverse Nodes in k-Group | Hard | 链表 | 分段翻转 |
| 31 | Next Permutation | Medium | 数组 | 找降序+交换+反转 |
| 32 | Longest Valid Parentheses | Hard | 栈 | 栈存下标 |
| 33 | Search in Rotated Sorted Array | Medium | 二分查找 | 判断有序段 |
| 34 | Find First and Last Position of Element in Sorted Array | Medium | 二分查找 | 两次二分找边界 |
| 35 | Search Insert Position | Easy | 二分查找 | 标准二分 |
| 39 | Combination Sum | Medium | 回溯 | 回溯+可重复选 |
| 41 | First Missing Positive | Hard | 数组 | 原地哈希 |
| 42 | Trapping Rain Water | Hard | 双指针 | 左右最大高度 |
| 45 | Jump Game II | Medium | 贪心 | 边界步数 |
| 46 | Permutations | Medium | 回溯 | visited 标记 |
| 48 | Rotate Image | Medium | 矩阵 | 对角线翻转+行反转 |
| 49 | Group Anagrams | Medium | 哈希表 | 排序后分组 |
| 51 | N-Queens | Hard | 回溯 | 列/对角线剪枝 |
| 53 | Maximum Subarray | Medium | 子数组 | Kadane 算法 |
| 54 | Spiral Matrix | Medium | 矩阵 | 四边界模拟 |
| 55 | Jump Game | Medium | 贪心 | 最远可达位置 |
| 56 | Merge Intervals | Medium | 数组 | 排序后合并 |
| 62 | Unique Paths | Medium | DP | 组合数/滚动数组 |
| 64 | Minimum Path Sum | Medium | DP | 原地 DP |
| 70 | Climbing Stairs | Easy | DP | 斐波那契 |
| 72 | Edit Distance | Hard | DP | 二维 DP |
| 73 | Set Matrix Zeroes | Medium | 矩阵 | 首行首列标记 |
| 74 | Search a 2D Matrix | Medium | 二分查找 | 一维化二分 |
| 75 | Sort Colors | Medium | 双指针 | 三指针荷兰国旗 |
| 76 | Minimum Window Substring | Hard | 滑动窗口 | 哈希表+双指针 |
| 78 | Subsets | Medium | 回溯 | 选或不选 |
| 79 | Word Search | Medium | 矩阵/回溯 | DFS+回溯 |
| 84 | Largest Rectangle in Histogram | Hard | 栈 | 单调栈 |
| 94 | Binary Tree Inorder Traversal | Easy | 二叉树 | 递归遍历 |
| 98 | Validate Binary Search Tree | Medium | 二叉搜索树 | 中序递增/边界递归 |
| 101 | Symmetric Tree | Easy | 二叉树 | 递归比较左右 |
| 102 | Binary Tree Level Order Traversal | Medium | 二叉树 | BFS 队列 |
| 104 | Maximum Depth of Binary Tree | Easy | 二叉树 | 递归深度 |
| 105 | Construct Binary Tree from Preorder and Inorder Traversal | Medium | 二叉树 | 递归构建 |
| 108 | Convert Sorted Array to Binary Search Tree | Easy | 二叉搜索树 | 取中间为根 |
| 114 | Flatten Binary Tree to Linked List | Medium | 二叉树 | 前序展开 |
| 118 | Pascal's Triangle | Easy | DP | 逐行迭代 |
| 121 | Best Time to Buy and Sell Stock | Easy | 贪心 | 记录最低价 |
| 124 | Binary Tree Maximum Path Sum | Hard | 二叉树 | 后序遍历 |
| 128 | Longest Consecutive Sequence | Medium | 哈希表 | 集合去重+连续查找 |
| 131 | Palindrome Partitioning | Medium | 回溯 | 回溯+回文判断 |
| 136 | Single Number | Easy | 哈希表/位运算 | 异或 |
| 138 | Copy List with Random Pointer | Medium | 链表 | 插入复制节点 |
| 139 | Word Break | Medium | DP | 枚举拆分点 |
| 141 | Linked List Cycle | Easy | 链表 | 快慢指针 |
| 142 | Linked List Cycle II | Medium | 链表 | 快慢指针找入环点 |
| 146 | LRU Cache | Medium | 设计/链表 | 双向链表+哈希表 |
| 148 | Sort List | Medium | 链表 | 归并排序 |
| 152 | Maximum Product Subarray | Medium | 子数组 | 同时维护最大最小值 |
| 153 | Find Minimum in Rotated Sorted Array | Medium | 二分查找 | 与右端点比较 |
| 155 | Min Stack | Medium | 栈 | 辅助栈 |
| 160 | Intersection of Two Linked Lists | Easy | 链表 | 双指针互换 |
| 169 | Majority Element | Easy | 数组 | Boyer-Moore 投票 |
| 189 | Rotate Array | Medium | 数组 | 三次反转 |
| 198 | House Robber | Medium | DP | 滚动变量 DP |
| 199 | Binary Tree Right Side View | Medium | 二叉树 | 层序取最右 |
| 200 | Number of Islands | Medium | 矩阵/图 | DFS 标记 |
| 206 | Reverse Linked List | Easy | 链表 | 迭代反转 |
| 207 | Course Schedule | Medium | 图 | 拓扑排序 |
| 208 | Implement Trie (Prefix Tree) | Medium | 设计/树 | 26 叉树 |
| 215 | Kth Largest Element in an Array | Medium | 堆 | 最小堆/快选 |
| 226 | Invert Binary Tree | Easy | 二叉树 | 递归交换 |
| 230 | Kth Smallest Element in a BST | Medium | 二叉搜索树 | 中序遍历 |
| 234 | Palindrome Linked List | Easy | 链表 | 快慢找中+反转 |
| 236 | Lowest Common Ancestor of a Binary Tree | Medium | 二叉树 | 递归查找 |
| 238 | Product of Array Except Self | Medium | 数组 | 左右前缀积 |
| 239 | Sliding Window Maximum | Hard | 滑动窗口 | 单调双端队列 |
| 240 | Search a 2D Matrix II | Medium | 矩阵 | 右上角行走 |
| 279 | Perfect Squares | Medium | DP | 完全背包 |
| 283 | Move Zeroes | Easy | 双指针 | 非零前移 |
| 287 | Find the Duplicate Number | Medium | 双指针 | 快慢指针（链表环） |
| 295 | Find Median from Data Stream | Hard | 堆 | 双堆平衡 |
| 300 | Longest Increasing Subsequence | Medium | DP | 贪心+二分 tails |
| 322 | Coin Change | Medium | DP | 完全背包 |
| 347 | Top K Frequent Elements | Medium | 堆 | 哈希统计+最小堆 |
| 394 | Decode String | Medium | 栈 | 双栈/单栈 |
| 416 | Partition Equal Subset Sum | Medium | DP | 0-1 背包 |
| 437 | Path Sum III | Medium | 二叉树 | 前缀和+DFS |
| 438 | Find All Anagrams in a String | Medium | 滑动窗口 | 固定窗口+哈希 |
| 543 | Diameter of Binary Tree | Easy | 二叉树 | 后序深度 |
| 560 | Subarray Sum Equals K | Medium | 哈希表 | 前缀和 |
| 739 | Daily Temperatures | Medium | 栈 | 单调递减栈 |
| 763 | Partition Labels | Medium | 贪心 | 记录最后位置+分段 |
| 994 | Rotting Oranges | Medium | 矩阵/图 | 多源 BFS |
| 1143 | Longest Common Subsequence | Medium | DP | 二维 DP |
