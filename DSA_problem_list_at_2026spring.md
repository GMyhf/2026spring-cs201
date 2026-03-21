## 2026spring 数算（DS Algo）每日选作
*Updated 2026-03-21 15:36 GMT+8*  *Compiled by Hongfei Yan (2026 Spring)*  
https://github.com/GMyhf/2026spring-cs201/blob/main/DSA_problem_list_at_2026spring.md

题解，https://fuynaloft.github.io/sol101/ ✅

<!--
|  |       |       | Medium |          |
-->



<!-- ### ==2026/03/02 -->

| 日期       | 问题编号与名称                 | 标签                                 | 难度 | 链接                                             |
| ---------- | ------------------------------ | ------------------------------------ | ---- | ------------------------------------------------ |
| 4~5月 | 3600.升级后最大生成树稳定性    | <mark>binary search</mark>, <mark>minimum spanning tree</mark> | Tough | https://leetcode.cn/problems/maximize-spanning-tree-stability-with-upgrades/          |
| 3~4月 | 3327.判断 DFS 字符串是否是回文串    | dfs, manacher  | Tough | https://leetcode.cn/problems/check-if-dfs-strings-are-palindromes/          |
|  |       |       | Medium |          |
| 0322 | 240.搜索二维矩阵II     | binary search    | Medium    | https://leetcode.cn/problems/search-a-2d-matrix-ii/      |
| 0322 | E3643.垂直翻转子矩阵   | two pinters, matrix  | Easy | https://leetcode.cn/problems/flip-square-submatrix-vertically/          |
| 0321 | 08210: 河中跳房子  | binary search, greedy  | Medium  | http://cs101.openjudge.cn/pctbook/M08210                         | 
| 0321 | 994.腐烂的橘子     | bfs    | Medium    | https://leetcode.cn/problems/rotting-oranges/ |
| 0320 | M3212.统计 X 和 Y 频数相等的子矩阵数量  | prefix sum  | Medium | https://leetcode.cn/problems/count-submatrices-with-equal-frequency-of-x-and-y/    |
| 0320 | 35.搜索插入位置  | binary search | Easy | https://leetcode.cn/problems/search-insert-position/         |
| 0319 | 03129:魔兽世界之一：备战 | implementation | Medium | http://cs101.openjudge.cn/practice/03129/          |
| 0319 | 2405.子字符串的最优划分  | greedy, bit manipulation | Medium | https://leetcode.cn/problems/optimal-partition-of-string/          |
| 0318 | 5.最长回文串 | DP, Center Expansion, Manacher | Medium | https://leetcode.cn/problems/longest-palindromic-substring/          |
| 0318 | 04093: 倒排索引查询 | Inverted Index | Medium | http://cs101.openjudge.cn/practice/04093/          |
| 0317 | 02406: 字符串乘方   | KMP  | Tough | http://cs101.openjudge.cn/practice/02406/          |
| 0317 | 39.组合总和   | backtracking    | Medium | https://leetcode.cn/problems/combination-sum/          |
| 0316 | 17.电话号码的字母组合    | backtracking       | Medium | https://leetcode.cn/problems/letter-combinations-of-a-phone-number/          |
| 0316 | 1415.长度为 n 的开心字符串中字典序第 k 小的字符串 | math, backtracking | Medium | https://leetcode.cn/problems/the-k-th-lexicographical-string-of-all-happy-strings-of-length-n/          |
| 0315 | 78.子集      | backtracking      | Medium | https://leetcode.cn/problems/subsets/         |
| 0315 | 46.全排列    | backtracking      | Medium | https://leetcode.cn/problems/permutations/          |
| 0314 | 20018:蚂蚁王国的越野跑  | <mark>merge sort</mark>, <mark>binary indexed tree</mark> | Medium2  | http://cs101.openjudge.cn/practice/20018/          |
| 0314 | 131.分割回文串       | dp, backtracking  | Medium | https://leetcode.cn/problems/palindrome-partitioning/          |
| 0313 | 30201: 旅行售货商问题  | <mark>bitmask dp</mark>  | Tough | http://cs101.openjudge.cn/practice/30201/       |
| 0313 | 04067: 回文数字 | queue       | Easy       | http://cs101.openjudge.cn/pctbook/E04067          |
| 0312 | 1888.使二进制字符串字符交替的最少反转次数 | <mark>sliding window</mark> | Medium2 | https://leetcode.cn/problems/minimum-number-of-flips-to-make-the-binary-string-alternating/ |
| 0312 | 1009.十进制整数的反码  | bit manipulation  | Easy | https://leetcode.cn/problems/complement-of-base-10-integer/description/          |
| 0311 | 2195.EIdiot First Search | <mark>dfs and similar, dp, trees</mark> | Tough | https://codeforces.com/problemset/problem/2195/E           |
| 0311 | 1364A.XXXXX  | two pointers  | 1200 | https://codeforces.com/problemset/problem/1364/A          |
| 0310 | 647.回文子串 | <mark>two pointers</mark>/中心扩散, dp/马拉车 | Medium | https://leetcode.cn/problems/palindromic-substrings/          |
| 0310 | M304.二维区域和检索 - 矩阵不可变 | prefix sum  | Medium | https://leetcode.cn/problems/range-sum-query-2d-immutable/          |
| 0309 | 1545. 找出第 N 个二进制字符串中的第 K 位 | dfs | Medium | https://leetcode.cn/problems/find-kth-bit-in-nth-binary-string/          |
| 0309 | E303.区域和检索 - 数组不可变    | <mark>prefix sum</mark> | Easy | https://leetcode.cn/problems/range-sum-query-immutable/         |
| 0308 | 01019:Number Sequence| <mark>binary search</mark> | Medium | http://cs101.openjudge.cn/practice/01019/          |
| 0308 | 20169:排队    | <mark>DSU</mark>       | Easy | http://cs101.openjudge.cn/practice/20169/          |
| 0307 | 07207:神奇的幻方  | <mark>matrix</mark>      | Medium | http://cs101.openjudge.cn/practice/07207/          |
| 0307 | 05467:多项式加法  | dict   | Easy | http://cs101.openjudge.cn/practice/05467/          |
| 0306 | 1461.检查一个字符串是否包含所有长度为 K 的二进制子串  | bit manipulation   | Medium | https://leetcode.cn/problems/check-if-a-string-contains-all-binary-codes-of-size-k/          |
| 0306 | 3827.统计单比特整数| bit manipulation  | Easy | https://leetcode.cn/problems/count-monobit-integers/          |
| 0305 | 1404.将二进制表示减到 1 的步骤数   | bit manipulation | Medium | https://leetcode.cn/problems/number-of-steps-to-reduce-a-number-in-binary-representation-to-one/    |
| 0305 | 868.二进制间距  | bit manipulation | Easy | https://leetcode.cn/problems/binary-gap/          |
| 0304 | 1680.连接连续二进制数字 | bit manipulation | Medium | https://leetcode.cn/problems/concatenation-of-consecutive-binary-numbers/          |
| 0304 | 1356.根据数字二进制下 1 的数目排序 | bit manipulation | Easy | https://leetcode.cn/problems/sort-integers-by-the-number-of-1-bits/          |
| 0303 | <mark>155.最小栈</mark> | OOP, 辅助栈 | Medium | https://leetcode.cn/problems/min-stack/ |
| 0303 |  190.颠倒二进制位  | <mark>bit manipulation</mark>   | Easy |  https://leetcode.cn/problems/reverse-bits/        |
| 0302 | 27300:模型整理 | sortings, AI   | Mediium   |  http://cs101.openjudge.cn/pctbook/M27300   |
| 0302 | 27653:Fraction类 | OOP   | Easy  | http://cs101.openjudge.cn/pctbook/E27653/  |
