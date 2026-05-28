# HW-2026上机练习6

*Updated 2026-05-28 16:51 GMT+8*
 *Compiled by Hongfei Yan (2026 Spring)*

http://forhuwei.openjudge.cn/2026practice6/

完成力理解中包含的12个题目，每个题目给出思路，C++代码及相应注释。

对12个题目，列表总结。

---

## 01: 01最小生成树

**思路：**
给定 n 个点的完全图，边权均为 0/1，仅有 m 条边权为 1。求最小生成树的边权和。
由于 0 权边数量极多（完全图减去 m 条边），我们求 0 权边构成的连通分量数。维护一个未访问集合，对每个点进行 BFS，只走 0 权边（即不通过给定的 1 权边连接的点）。设连通分量数为 comp，则答案为 comp - 1。

**代码：**
```cpp
#include <bits/stdc++.h>
using namespace std;

int main() {
    ios::sync_with_stdio(false);
    cin.tie(nullptr);

    int n, m;
    cin >> n >> m;
    vector<vector<int>> adj(n);
    for (int i = 0; i < m; i++) {
        int a, b;
        cin >> a >> b;
        a--; b--;
        adj[a].push_back(b);
        adj[b].push_back(a);
    }

    set<int> unvisited;
    for (int i = 0; i < n; i++) unvisited.insert(i);

    int components = 0;
    for (int start = 0; start < n; start++) {
        if (unvisited.count(start)) {
            components++;
            vector<int> stk = {start};
            unvisited.erase(start);
            while (!stk.empty()) {
                int u = stk.back(); stk.pop_back();
                unordered_set<int> bad(adj[u].begin(), adj[u].end());
                vector<int> to_remove;
                for (int v : unvisited)
                    if (!bad.count(v)) to_remove.push_back(v);
                for (int v : to_remove) {
                    unvisited.erase(v);
                    stk.push_back(v);
                }
            }
        }
    }
    cout << components - 1 << "\n";
    return 0;
}
```

## 02: 货运费用

**思路：**
N 个城市，M 条双向路，每条路有重量限制 L。货车总重 W，需要从 1 到 N，求最少过路费（每条路 1 元）。若无法到达输出 -1。
只考虑限制 L >= W 的路，在该子图上用 BFS 求 1 到 N 的最短距离（边数）。

**代码：**
```cpp
#include <bits/stdc++.h>
using namespace std;

int main() {
    ios::sync_with_stdio(false);
    cin.tie(nullptr);

    int N, M, W;
    cin >> N >> M >> W;
    vector<vector<int>> adj(N);
    for (int i = 0; i < M; i++) {
        int A, B, L;
        cin >> A >> B >> L;
        if (L >= W) {
            adj[A-1].push_back(B-1);
            adj[B-1].push_back(A-1);
        }
    }

    vector<int> dist(N, -1);
    dist[0] = 0;
    queue<int> q;
    q.push(0);
    while (!q.empty()) {
        int u = q.front(); q.pop();
        for (int v : adj[u]) {
            if (dist[v] == -1) {
                dist[v] = dist[u] + 1;
                q.push(v);
            }
        }
    }
    cout << dist[N-1] << "\n";
    return 0;
}
```

## 03: NOIP2014TG-2 联合权值 

这是一个经典的树形图遍历与组合计数问题。由于图是一个无向连通图且有 $n$ 个点和 $n-1$ 条边，因此该图是一棵树。

### 解题思路

对于图中的任意两个距离为 2 的点对 $(u, v)$，它们在树中必然拥有一个共同的相邻节点 $x$。也就是说，它们满足 $u \to x \to v$ 的关系。

因此，我们可以枚举每一个节点 $x$，将 $x$ 作为“中间节点”，然后考虑 $x$ 的所有邻居节点之间的两两组合。

假设节点 $x$ 的邻居节点集合为 $N(x)$，其邻居节点的权值分别为 $V_1, V_2, \dots, V_k$（其中 $k$ 为 $x$ 的度数）：

1. **求最大联合权值：**
   对于中间节点 $x$，其邻居中能产生的最大联合权值，显然是所有邻居中**权值最大**和**权值次大**的两个节点的权值乘积。我们可以在 $O(k)$ 的时间内找出这两个最大值并更新全局最大值。

2. **求联合权值之和：**
   所有邻居两两权值乘积的和为：
   $$ \sum_{1 \le i \neq j \le k} V_i V_j $$
   根据代数恒等式，我们可以将其转化为：
   $$ \sum_{1 \le i \neq j \le k} V_i V_j = \left( \sum_{i=1}^k V_i \right)^2 - \sum_{i=1}^k V_i^2 $$
   这同样可以在 $O(k)$ 的时间内计算出来，其中求和过程需要对 $10007$ 取模。

由于每个节点的度数之和等于边数的两倍（即 $2n - 2$），因此整体的时间复杂度为 $O(n)$，空间复杂度也为 $O(n)$。

### C++ 代码实现

```cpp
#include <iostream>
#include <vector>
#include <algorithm>

using namespace std;

const int MOD = 10007;

int main() {
    // 优化输入输出效率
    ios_base::sync_with_stdio(false);
    cin.tie(NULL);

    int n;
    if (!(cin >> n)) return 0;

    // 使用邻接表存图
    vector<vector<int>> adj(n + 1);
    for (int i = 0; i < n - 1; ++i) {
        int u, v;
        cin >> u >> v;
        adj[u].push_back(v);
        adj[v].push_back(u);
    }

    // 读入点权
    vector<long long> w(n + 1);
    for (int i = 1; i <= n; ++i) {
        cin >> w[i];
    }

    long long max_joint = 0;
    long long sum_joint = 0;

    // 枚举每一个点作为中间节点
    for (int i = 1; i <= n; ++i) {
        if (adj[i].size() < 2) continue; // 邻居数少于2则无法形成距离为2的点对

        long long max1 = 0, max2 = 0;
        long long sum_w = 0;
        long long sum_w2 = 0;

        for (int neighbor : adj[i]) {
            long long val = w[neighbor];
            
            // 寻找最大值和次大值
            if (val > max1) {
                max2 = max1;
                max1 = val;
            } else if (val > max2) {
                max2 = val;
            }
            
            // 累计和与平方和
            sum_w = (sum_w + val) % MOD;
            sum_w2 = (sum_w2 + val * val) % MOD;
        }

        // 更新最大联合权值
        long long current_max = max1 * max2;
        if (current_max > max_joint) {
            max_joint = current_max;
        }

        // 计算当前节点贡献的联合权值之和：(sum_w^2 - sum_w2) % MOD
        long long term = (sum_w * sum_w - sum_w2) % MOD;
        if (term < 0) {
            term += MOD;
        }
        sum_joint = (sum_joint + term) % MOD;
    }

    cout << max_joint << " " << sum_joint << "\n";

    return 0;
}
```

### 复杂度分析

- **时间复杂度：** $O(n)$。我们遍历了每个节点及其所有的出边。每个邻接点被访问了常数次，因此总运行时间与图的边数和点数呈线性关系。
- **空间复杂度：** $O(n)$。用于存储邻接表以及每个节点的权值。符合题目中 $131072\text{ kB}$（约 $128\text{ MB}$）的内存限制。



## 04: 巨大的圣诞树

**思路：**
选择若干边构成一棵以 1 为根的树。边 (a,b) 代价 = 单价 × 远离根一侧的子树权重和。
等价于：总代价 = `sum_{v ≠ 1} weight_subtree(v) × price(parent(v),v) = sum_v w_v × (sum_{e on path 1→v} price_e)`。
因此只需以单价为边权，从根 1 运行 Dijkstra 求最短路径，最终总代价 = `sum_v weight_v × dist[v]`。若图不连通则输出 "No Answer"。

**代码：**
```cpp
#include <bits/stdc++.h>
using namespace std;
using ll = long long;

int main() {
    ios::sync_with_stdio(false);
    cin.tie(nullptr);

    int T;
    cin >> T;
    while (T--) {
        int v, e;
        cin >> v >> e;
        if (v == 0) { cout << "0\n"; continue; }
        vector<int> weights(v);
        for (int i = 0; i < v; i++) cin >> weights[i];
        vector<vector<pair<int,int>>> adj(v);
        for (int i = 0; i < e; i++) {
            int a, b, c;
            cin >> a >> b >> c;
            adj[a-1].push_back({b-1, c});
            adj[b-1].push_back({a-1, c});
        }

        const ll INF = 1e18;
        vector<ll> dist(v, INF);
        dist[0] = 0;
        priority_queue<pair<ll,int>, vector<pair<ll,int>>, greater<>> pq;
        pq.push({0, 0});
        while (!pq.empty()) {
            auto [d, u] = pq.top(); pq.pop();
            if (d != dist[u]) continue;
            for (auto [to, price] : adj[u]) {
                ll nd = d + price;
                if (nd < dist[to]) {
                    dist[to] = nd;
                    pq.push({nd, to});
                }
            }
        }

        bool ok = true;
        for (int i = 1; i < v; i++) if (dist[i] == INF) { ok = false; break; }
        if (!ok) cout << "No Answer\n";
        else {
            ll ans = 0;
            for (int i = 0; i < v; i++) ans += (ll)weights[i] * dist[i];
            cout << ans << "\n";
        }
    }
    return 0;
}
```

## 05: 电话线路

**思路：**
从 1 到 N 的路径上，可以指定最多 K 条电缆免费，只需支付剩余电缆中最贵的那条。求最小费用。
二分答案 X，判断是否存在一条路径，其中价格 > X 的边数 ≤ K。将 > X 的边权视为 1，≤ X 的边权视为 0，用 0-1 BFS 求最短路径，若距离 ≤ K 则可行。

**代码：**
```cpp
#include <bits/stdc++.h>
using namespace std;

int main() {
    ios::sync_with_stdio(false);
    cin.tie(nullptr);

    int N, P, K;
    cin >> N >> P >> K;
    vector<vector<pair<int,int>>> adj(N);
    for (int i = 0; i < P; i++) {
        int A, B, L;
        cin >> A >> B >> L;
        adj[A-1].push_back({B-1, L});
        adj[B-1].push_back({A-1, L});
    }

    auto check = [&](int X) -> bool {
        vector<int> dist(N, INT_MAX);
        dist[0] = 0;
        deque<int> dq;
        dq.push_back(0);
        while (!dq.empty()) {
            int u = dq.front(); dq.pop_front();
            for (auto [v, L] : adj[u]) {
                int cost = (L > X) ? 1 : 0;
                if (dist[u] + cost < dist[v]) {
                    dist[v] = dist[u] + cost;
                    if (cost == 0) dq.push_front(v);
                    else dq.push_back(v);
                }
            }
        }
        return dist[N-1] <= K;
    };

    int lo = -1, hi = 1000001;
    while (hi - lo > 1) {
        int mid = (lo + hi) / 2;
        if (check(mid)) hi = mid;
        else lo = mid;
    }
    cout << (hi <= 1000000 ? hi : -1) << "\n";
    return 0;
}
```

## 06: CSP-J2023-4 旅游巴士

**思路：**
有向图，从 1 到 n。到达 1 和离开 n 的时间必须是 k 的倍数，每条边只能在不早于 a_i 时刻通过，且任何地点不可停留。
`dist[node][mod]` = 当前在 node 且已走步数 mod k = mod 时的最早时刻。用 Dijkstra 扩展：对边 (u,v,a)，若当前时刻 t ≥ a 则直接走，否则需将出发时间"延后"至下一个满足 ≥ a 的 k 的倍数时刻（即加 wait = ceil((a-t)/k)*k）。最终答案为 `dist[n-1][0]`（长度是 k 的倍数）。

**代码：**
```cpp
#include <bits/stdc++.h>
using namespace std;
using ll = long long;

int main() {
    ios::sync_with_stdio(false);
    cin.tie(nullptr);

    int n, m, k;
    cin >> n >> m >> k;
    vector<vector<pair<int,int>>> adj(n);
    for (int i = 0; i < m; i++) {
        int u, v, a;
        cin >> u >> v >> a;
        adj[u-1].push_back({v-1, a});
    }

    const ll INF = 1e18;
    vector<vector<ll>> dist(n, vector<ll>(k, INF));
    dist[0][0] = 0;
    using State = tuple<ll,int,int>;
    priority_queue<State, vector<State>, greater<State>> pq;
    pq.push({0, 0, 0});

    while (!pq.empty()) {
        auto [t, u, mod] = pq.top(); pq.pop();
        if (t != dist[u][mod]) continue;
        for (auto [v, a] : adj[u]) {
            ll nt;
            if (t >= a) nt = t + 1;
            else {
                ll wait = ((a - t + k - 1) / k) * k;
                nt = t + wait + 1;
            }
            int nm = (mod + 1) % k;
            if (nt < dist[v][nm]) {
                dist[v][nm] = nt;
                pq.push({nt, v, nm});
            }
        }
    }

    ll ans = dist[n-1][0];
    cout << (ans == INF ? -1 : ans) << "\n";
    return 0;
}
```

## 07: 李太白饮酒(Drink Drink)

**思路：**
经典"打家劫舍"问题。n 个酒馆，不能买相邻两家的酒，求最大满意度之和。
dp[i] = 前 i 个酒馆的最大满意度，dp[i] = max(dp[i-1], dp[i-2] + satisfaction[i])。

**代码：**
```cpp
#include <bits/stdc++.h>
using namespace std;

int main() {
    ios::sync_with_stdio(false);
    cin.tie(nullptr);

    int n;
    cin >> n;
    vector<int> sat(n);
    for (int i = 0; i < n; i++) cin >> sat[i];

    if (n == 0) cout << "0\n";
    else if (n == 1) cout << sat[0] << "\n";
    else {
        vector<int> dp(n);
        dp[0] = sat[0];
        dp[1] = max(sat[0], sat[1]);
        for (int i = 2; i < n; i++)
            dp[i] = max(dp[i-1], dp[i-2] + sat[i]);
        cout << dp[n-1] << "\n";
    }
    return 0;
}
```

## 08: 最优包含

**思路：**
最少修改 S 中的多少个字符，使得 T 是 S 的子序列。
`dp[i][j]` = 用 S 的前 i 个字符匹配 T 的前 j 个字符所需的最少修改次数。

- 若 S[i-1] == T[j-1]：`dp[i][j] = dp[i-1][j-1]`（直接匹配）
- 否则：`dp[i][j] = min(dp[i-1][j]（跳过 S[i-1]）, dp[i-1][j-1] + 1（修改 S[i-1]）)`

**代码：**
```cpp
#include <bits/stdc++.h>
using namespace std;

int main() {
    ios::sync_with_stdio(false);
    cin.tie(nullptr);

    string S, T;
    cin >> S >> T;
    int n = S.size(), m = T.size();

    const int INF = 1e9;
    vector<vector<int>> dp(n + 1, vector<int>(m + 1, INF));
    for (int i = 0; i <= n; i++) dp[i][0] = 0;

    for (int i = 1; i <= n; i++) {
        for (int j = 1; j <= min(i, m); j++) {
            if (S[i-1] == T[j-1])
                dp[i][j] = dp[i-1][j-1];
            else
                dp[i][j] = min(dp[i-1][j], dp[i-1][j-1] + 1);
        }
    }
    cout << dp[n][m] << "\n";
    return 0;
}
```

## 09: 山区建小学

**思路：**
m 个村庄在一条直线上，选择 n 个建小学，使所有村到最近小学的距离和最小。
预处理 `cost[i][j]` = 村庄 i~j 只建一所小学的最小距离和（选在中位数位置）。`dp[k][i]` = 前 i 个村庄建 k 所小学的最小距离和。转移：`dp[k][i] = min_{j < i} dp[k-1][j] + cost[j+1][i]`。

**代码：**
```cpp
#include <bits/stdc++.h>
using namespace std;

int main() {
    ios::sync_with_stdio(false);
    cin.tie(nullptr);

    int m, n;
    cin >> m >> n;
    vector<int> d(m - 1);
    for (int i = 0; i < m - 1; i++) cin >> d[i];

    vector<int> pos(m + 1, 0);
    for (int i = 2; i <= m; i++) pos[i] = pos[i-1] + d[i-2];

    vector<vector<int>> cost(m + 1, vector<int>(m + 1, 0));
    for (int i = 1; i <= m; i++)
        for (int j = i; j <= m; j++) {
            int mid = (i + j) / 2;
            for (int k = i; k <= j; k++)
                cost[i][j] += abs(pos[k] - pos[mid]);
        }

    const int INF = 1e9;
    vector<vector<int>> dp(n + 1, vector<int>(m + 1, INF));
    dp[0][0] = 0;
    for (int i = 1; i <= m; i++) dp[1][i] = cost[1][i];

    for (int k = 2; k <= n; k++)
        for (int i = 1; i <= m; i++)
            for (int j = k - 1; j < i; j++)
                dp[k][i] = min(dp[k][i], dp[k-1][j] + cost[j+1][i]);

    cout << dp[n][m] << "\n";
    return 0;
}
```

## 10: [USACO 2019 Jan Platinum P1] Redistricting

这道题可以通过**单调队列优化的动态规划（DP）**在 $O(N)$ 的时间复杂度内解决。

### 算法分析

首先，我们将问题进行数学转化。对于每个位置的奶牛，如果是 'G'（更赛牛），我们记为 $+1$；如果是 'H'（荷斯坦牛），我们记为 $-1$。记前缀和为 $S_i$（其中 $S_0 = 0$）。

对于一个从 $j+1$ 到 $i$ 的区间（长度不超过 $K$）：
* 更赛牛的数量不少于荷斯坦牛的数量，等价于该区间的和大于等于 $0$，即 $S_i - S_j \ge 0 \iff S_i \ge S_j$。此时该分区是“更赛牛较多或均势”的，代价增加 $1$。
* 反之，若 $S_i < S_j$，该分区是好的，代价增加 $0$。

设 $dp[i]$ 表示对前缀 $1 \dots i$ 进行合法划分，能得到的“更赛牛较多或均势”分区的最小数量。
显然有状态转移方程：
$$dp[i] = \min_{i-K \le j < i} \left( dp[j] + [S_i \ge S_j] \right)$$
其中 $[S_i \ge S_j]$ 在条件成立时为 $1$，否则为 $0$。

因为 $N \le 3 \cdot 10^5$，直接 $O(NK)$ 转移会超时。我们需要优化转移过程。

#### 单调队列优化
观察转移式，对于当前的 $i$：
1. 决策点 $j$ 的范围是滑动窗口 $[i-K, i-1]$。
2. 我们希望 $dp[j] + [S_i \ge S_j]$ 尽量小。
3. 显然，若存在两个决策点 $j_1 < j_2$，且满足以下条件之一，则 $j_2$ 优于 $j_1$：
   * $dp[j_2] < dp[j_1]$ （$dp$ 值更小，肯定更优）
   * $dp[j_2] == dp[j_1]$ 且 $S_{j_2} > S_{j_1}$ （在 $dp$ 相同的情况下，前缀和越大，越容易满足 $S_i < S_{j_2}$ 从而不产生额外代价 $1$）

根据上述偏序关系，我们可以维护一个单调队列（双端队列 `std::deque`）：
* 队列中保存决策点的下标，队列中的元素按照 $dp[j]$ 单调递增；在 $dp[j]$ 相等时，按照 $S[j]$ 单调递减。
* 这样，队列的头部（`dq.front()`）永远是当前窗口内最优秀的决策点。
* 每次计算 $dp[i]$ 时，直接取队首元素进行转移。
* 随后将新的状态 $i$ 放入队列尾部，并在放入前弹出队列中所有不如 $i$ 优秀的决策点。

### C++ 代码实现

```cpp
#include <iostream>
#include <vector>
#include <string>
#include <deque>

using namespace std;

int main() {
    // 优化输入输出效率
    ios_base::sync_with_stdio(false);
    cin.tie(NULL);

    int n, k;
    if (!(cin >> n >> k)) return 0;

    string s;
    cin >> s;

    // S[i] 表示前 i 个奶牛的前缀和 (G 为 1, H 为 -1)
    vector<int> S(n + 1, 0);
    for (int i = 0; i < n; ++i) {
        S[i + 1] = S[i] + (s[i] == 'G' ? 1 : -1);
    }

    vector<int> dp(n + 1, 0);
    deque<int> dq;
    
    // 初始状态，0 是一个合法的决策点
    dq.push_back(0);

    for (int i = 1; i <= n; ++i) {
        // 1. 移出窗口之外的决策点
        while (!dq.empty() && dq.front() < i - k) {
            dq.pop_front();
        }

        // 2. 队首即为最优决策点
        int best = dq.front();
        dp[i] = dp[best] + (S[i] >= S[best] ? 1 : 0);

        // 3. 维护队列的单调性
        while (!dq.empty()) {
            int back = dq.back();
            // 如果队尾决策点的 dp 值比当前更大，
            // 或者 dp 值相同但 S 值比当前更小（或相等），则该队尾决策点被淘汰
            if (dp[back] > dp[i] || (dp[back] == dp[i] && S[back] <= S[i])) {
                dq.pop_back();
            } else {
                break;
            }
        }
        
        // 4. 将当前点作为决策点加入队列
        dq.push_back(i);
    }

    cout << dp[n] << "\n";

    return 0;
}
```

### 复杂度分析
* **时间复杂度**：每个索引最多入队一次、出队一次，因此整体时间复杂度为 $O(N)$，可以轻松通过所有测试点。
* **空间复杂度**：由于只使用了大小为 $N$ 的数组以及单调队列，空间复杂度为 $O(N)$。



## 11: XY操作

**思路：**
长度为 n 的数字序列，每次对最左两个数做 (a+b)%10 或 (a*b)%10，直到剩一个数。求所有 2^(n-1) 种操作序列中最终值为 K 的方案数。
从左到右线性 DP。dp[v] = 当前结果值为 v 的方案数。初始 dp[A[0]] = 1。处理每个新数字 x 时，对每个 v 分别尝试 X 操作和 Y 操作，累加到 ndp。

**代码：**
```cpp
#include <bits/stdc++.h>
using namespace std;

const int MOD = 998244353;

int main() {
    ios::sync_with_stdio(false);
    cin.tie(nullptr);

    int n;
    cin >> n;
    vector<int> A(n);
    for (int i = 0; i < n; i++) cin >> A[i];

    vector<int> dp(10, 0);
    dp[A[0]] = 1;

    for (int idx = 1; idx < n; idx++) {
        int x = A[idx];
        vector<int> ndp(10, 0);
        for (int v = 0; v < 10; v++) {
            if (dp[v] == 0) continue;
            ndp[(v + x) % 10] = (ndp[(v + x) % 10] + dp[v]) % MOD;
            ndp[(v * x) % 10] = (ndp[(v * x) % 10] + dp[v]) % MOD;
        }
        dp = move(ndp);
    }

    for (int k = 0; k < 10; k++)
        cout << dp[k] << "\n";
    return 0;
}
```



## 12: [USACO 2024 Open Gold P3] Smaller Averages

这是一道非常经典的动态规划（DP）优化问题。

### 解题思路

#### 1. 状态定义与朴素转移
我们希望求出将数组 $A[1 \dots N]$ 和 $B[1 \dots N]$ 划分为相同段数且满足均值限制的方法数。

设 $DP[i][j]$ 表示将 $A$ 的前缀 $A[1 \dots i]$ 和 $B$ 的前缀 $B[1 \dots j]$ 进行合法划分（段数相同）的方案数。

考虑最后一段的划分位置：
* 设 $A$ 的最后一段为 $A[x \dots i]$（其中 $1 \le x \le i$），其平均值为 $U(x, i) = \frac{\sum_{k=x}^i a_k}{i - x + 1}$。
* 设 $B$ 的最后一段为 $B[y \dots j]$（其中 $1 \le y \le j$），其平均值为 $V(y, j) = \frac{\sum_{k=y}^j b_k}{j - y + 1}$。

那么状态转移方程为：
$$DP[i][j] = \sum_{x=1}^i \sum_{y=1}^j DP[x-1][y-1] \cdot [U(x, i) \le V(y, j)]$$

其中 $[U(x, i) \le V(y, j)]$ 为条件指示函数，当条件成立时为 $1$，否则为 $0$。
基础状态为 $DP[0][0] = 1$，其余 $DP[0][j] = 0$ 且 $DP[i][0] = 0$。

如果直接按照上述方程计算，状态数有 $O(N^2)$，每个状态转移需要 $O(N^2)$ 的时间，总时间复杂度为 $O(N^4)$。在 $N \le 500$ 的数据范围内会超时。我们需要对其进行优化。

#### 2. 优化转移到 $O(N^3)$
注意到，当我们固定 $i$ 时，对于所有的 $x \in [1, i]$，$U(x, i)$ 的值是确定的，共有 $i$ 个可能的值。
同样，对于固定的 $j$，对于所有的 $y \in [1, j]$，$V(y, j)$ 的值也是确定的。

我们可以将这些均值进行排序。
1. **预处理 $B$ 的均值：**
   对于每个 $j \in [1, N]$，我们可以把所有 $y \in [1, j]$ 对应的 $V(y, j)$ 和其对应的原下标 $y$ 组合，按 $V(y, j)$ 的值从小到大排序。
   这个预处理的时间复杂度为 $\sum_{j=1}^N O(j \log j) = O(N^2 \log N)$。

2. **在 DP 过程中优化 $A$ 的均值：**
   当我们外层循环枚举到 $i$ 时，我们将所有的 $U(x, i)$（$x \in [1, i]$）从小到大排序。记排序后的序列为 $u_1 \le u_2 \le \dots \le u_i$，对应的原左端点为 $posU_1, posU_2, \dots, posU_i$。
   由于 $U$ 是排好序的，对于任何给定的 $V(y, j)$，满足 $U(x, i) \le V(y, j)$ 的 $x$ 在排序后的序列中必定对应一个**前缀** $1 \dots ptr$。
   也就是说，只要我们能找到最大的 $ptr$ 使得 $u_{ptr} \le V(y, j)$，那么该项对应的累加值就是：
   $$\sum_{m=1}^{ptr} DP[posU_m - 1][y - 1]$$
   
   我们可以定义前缀和数组 `pref[y][ptr]` 表示前 $ptr$ 个排好序的 $x$ 对应的 $DP[posU_m - 1][y - 1]$ 的和。
   因为 $i$ 是固定的，对于所有的 $y \in [1, N]$，我们可以用 $O(N \cdot i)$ 的时间预处理出所有的 `pref[y][ptr]`：
   $$pref[y][ptr] = pref[y][ptr-1] + DP[posU_{ptr}-1][y-1]$$

3. **双指针加速计算：**
   对于固定的 $i$ 和 $j$，由于预处理时我们已经把 $V(y, j)$ 按照大小排好序了，当我们在排好序的 $V(y, j)$ 中升序遍历时，满足 $u_{ptr} \le V(y, j)$ 的分界点 $ptr$ 也是**单调不降**的。
   因此，我们可以使用**双指针**在 $O(i + j)$ 的时间内为所有 $y \in [1, j]$ 找到对应的 $ptr$，并将对应的 `pref[y][ptr]` 累加到 $DP[i][j]$ 中。
   
   对于每个 $i$，内层循环枚举 $j$，总共需要的时间为 $\sum_{j=1}^N O(i + j) = O(N^2)$。
   外层循环 $i$ 迭代 $N$ 次，因此总的时间复杂度可以被优化至 $O(N^3)$。

#### 3. 精度处理
因为 $N \le 500$ 且元素最大值为 $10^6$，均值的最小可能非零差值为 $\frac{1}{500 \times 500} = 4 \times 10^{-6}$。
双精度浮点数 `double` 拥有 53 位的精度（约为 15-17 位十进制有效数字），因此使用 `double` 存储均值并引入一个很小的 $\epsilon = 10^{-11}$ 进行比较，可以保证精度安全，并且避免了复杂的繁分数比较。

### C++ 代码实现

```cpp
#include <iostream>
#include <vector>
#include <algorithm>

using namespace std;

const int MOD = 1e9 + 7;
const double EPS = 1e-11;

int N;
int A[505], B[505];
long long PA[505], PB[505];

// 存储均值及其原下标的结构体
struct ValIdx {
    double val;
    int idx;
    bool operator<(const ValIdx& other) const {
        return val < other.val;
    }
};

ValIdx V_sorted[505][505];
int dp[505][505];
int pref[505][505];

int main() {
    // 提高输入输出效率
    ios_base::sync_with_stdio(false);
    cin.tie(NULL);

    if (!(cin >> N)) return 0;

    for (int i = 1; i <= N; ++i) {
        cin >> A[i];
        PA[i] = PA[i - 1] + A[i];
    }
    for (int i = 1; i <= N; ++i) {
        cin >> B[i];
        PB[i] = PB[i - 1] + B[i];
    }

    // 预处理并排序 B 的所有区间均值
    for (int j = 1; j <= N; ++j) {
        for (int y = 1; y <= j; ++y) {
            double avg = (double)(PB[j] - PB[y - 1]) / (j - y + 1);
            V_sorted[j][y - 1] = {avg, y};
        }
        sort(V_sorted[j], V_sorted[j] + j);
    }

    // 初始化 DP 边界
    dp[0][0] = 1;

    for (int i = 1; i <= N; ++i) {
        // 计算并排序当前 A 的所有以 i 结尾的区间均值
        vector<ValIdx> u(i + 1);
        for (int x = 1; x <= i; ++x) {
            double avg = (double)(PA[i] - PA[x - 1]) / (i - x + 1);
            u[x] = {avg, x};
        }
        sort(u.begin() + 1, u.end());

        // 预处理前缀和 pref[y][m]
        for (int y = 1; y <= N; ++y) {
            pref[y][0] = 0;
            for (int m = 1; m <= i; ++m) {
                pref[y][m] = (pref[y][m - 1] + dp[u[m].idx - 1][y - 1]) % MOD;
            }
        }

        // 双指针转移计算 dp[i][j]
        for (int j = 1; j <= N; ++j) {
            int ptr = 0;
            long long sum = 0;
            for (int r = 0; r < j; ++r) {
                double v_val = V_sorted[j][r].val;
                int y = V_sorted[j][r].idx;
                // 利用单调性移动指针
                while (ptr < i && u[ptr + 1].val <= v_val + EPS) {
                    ptr++;
                }
                sum = (sum + pref[y][ptr]) % MOD;
            }
            dp[i][j] = sum;
        }
    }

    cout << dp[N][N] << "\n";

    return 0;
}
```



---

## 题目总结

| # | 题号 | 标题 | 核心算法 | 难度 |
|---|------|------|----------|------|
| 1 | 01 | 01最小生成树 | BFS/并查集（补图连通分量） | 中等 |
| 2 | 02 | 货运费用 | BFS 最短路 + 条件过滤 | 简单 |
| 3 | 03 | NOIP2014TG-2 联合权值 | 树 + 枚举中间节点 | 中等 |
| 4 | 04 | 巨大的圣诞树 | Dijkstra 最短路径树 | 中等 |
| 5 | 05 | 电话线路 | 二分答案 + 0-1 BFS | 中等 |
| 6 | 06 | CSP-J2023-4 旅游巴士 | Dijkstra 分层图 (mod k) | 较难 |
| 7 | 07 | 李太白饮酒(Drink Drink) | DP（打家劫舍） | 简单 |
| 8 | 08 | 最优包含 | DP（编辑距离变体） | 中等 |
| 9 | 09 | 山区建小学 | 区间 DP + 中位数 | 中等 |
| 10 | 10 | [USACO] Redistricting | DP + 线段树/滑动窗口 | 较难 |
| 11 | 11 | XY操作 | 线性 DP | 中等 |
| 12 | 12 | [USACO] Smaller Averages | 2D DP | 较难 |
