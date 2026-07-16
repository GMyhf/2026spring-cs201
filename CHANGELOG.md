# 更新日志 / Changelog

本文档记录课件（Markdown 笔记）的重要修订。时间为 GMT+8（北京时间）。

---

## 2026-06-18 ~ 2026-06-19 — 课件校对（Proofreading pass）

对课程讲义、写作题库等 Markdown 笔记进行了一轮系统性校对。本轮改动以排版规范化与错别字订正为主，**不改变任何技术内容与题解结论**。主要类别如下：

- **中英文/数字间距**：在中文与英文单词、数字之间统一补加空格，例如
  `Python是` → `Python 是`、`2026spring` → `2026 spring`、`OOP及` → `OOP 及`、
  `入度为0` → `入度为 0`、`DFS或BFS` → `DFS 或 BFS`。
- **错别字与拼写订正**：
  - 英文：`Complied` → `Compiled`、`Karn` → `Kahn`、`Intialize` → `Initialize`、
    `Thmos.H.Cormen` → `Thomas H. Cormen`。
  - 中文：`其它` → `其他`、`储存` → `存储`、`邻接列表` → `邻接表`、
    `宽度优先搜索` → `广度优先搜索`、`树节无树` → `树结无树`、`多家练习` → `多加练习`、
    `加人该棵树` → `加入该棵树`、`写的比较随意` → `写得比较随意`。
- **标点规范化**：全角句点 `．` → `。`，省略号 `。。。` → `……`，
  以及全角拉丁字母（`Ｌ`、`Ｑ` 等）改为半角。
- **数学公式修复**：将失效的 GIF/base64 内联图片公式改为 LaTeX 记法，例如
  `{0,11,45,81}![...]` → `$\{0,11,45,81\}$`、`𝑂(𝑛×𝑘)` → `$O(n \times k)$`。
- **代码格式**：修正行末反斜杠续行为括号换行（`knight_tour` 示例），
  统一行内代码反引号与 `stack + DFS` 类术语间距。
- **文字清理**：删除个别已废弃的删除线编辑批注；对少数表述补充说明
  （如满 m 叉树题目补充“根为第一层”前提）。

### 涉及文件

| 文件 | 说明 |
| ---- | ---- |
| `202603_DSA_W01_OOP.md` | 第 1 周 OOP 与 Python 基础 |
| `202603_DSA_W02_BIT_Fenwick.md` | 第 2 周 树状数组（Fenwick / BIT） |
| `202603_DSA_W03_KMP_InvertedIndex_BitOpt.md` | 第 3 周 KMP、倒排索引、位运算优化 |
| `202603_DSA_W04-5.5_Complexity_LinearStructures.md` | 第 4–5.5 周 复杂度与线性结构 |
| `202603_DSA_W5.5_VM_Shell_LLMs.md` | 第 5.5 周 虚拟机、Shell、LLM |
| `202603_DSA_AI_literacy.md` | AI 素养 |
| `202604_DSA_W06-08_Tree.md` | 第 6–8 周 树 |
| `202604_DSA_W09-12_Graph.md` | 第 9–12 周 图论 |
| `202606_DSA_W14_Final_Exam_Review.md` | 第 14 周 期末复习 |
| `20250520_HashTable.md` | 哈希表 |
| `DSA_MOOC_solution.md` | MOOC 题解 |
| `DSA_problem_list_at_2026spring.md` | 每日选作题目列表 |
| `LC_top-100-liked.md` | LeetCode 热题 100 |
| `written_exam_DSA-B.md` | 笔试题（含解答） |
| `written_exam_DSA-B_nosolution.md` | 笔试题（无解答） |

### 相关提交

- `c8266fd` Proofread week 1 OOP notes
- `4682a4e` Proofread remaining root markdown notes
- `cc4d2a7` Proofread tree data structure notes
- `686b022` Proofread graph theory notes
- `3164610` / `775330f` Proofread DSA written exam materials
- `7a8d6ec` Proofread DSA written exam without solutions
