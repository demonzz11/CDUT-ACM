# E - Max Matrix 2

## 题目描述

给定两个整数 $n$ 和 $m$，以及两个数组 $X$ 和 $Y$：

- $X[i]$ 表示矩阵第 $i$ 行的最大值
- $Y[j]$ 表示矩阵第 $j$ 列的最大值

要求使用 $1$ 到 $n \times m$ 的所有整数（每个数字恰好使用一次）构造一个 $n \times m$ 的矩阵，满足上述条件。

## 解题思路

### 核心观察

1. **唯一性约束**：如果同一个值在 $X$ 数组或 $Y$ 数组中出现多次，则无解
2. **最小值约束**：对于位置 $(i, j)$，其能填写的最大值不超过 $\min(X[i], Y[j])$
3. **贪心策略**：从大到小处理数字，优先放置较大值

### 四种情况分析

设当前要填写的数字为 v：

#### 情况 1：v 同时出现在 $X$ 和 $Y$ 中

- **约束**：必须在位置 $(i, j)$ 填写 $v$，其中 $X[i] = v$ 且 $Y[j] = v$
- **原因**：这是唯一能同时满足第 $i$ 行和第 $j$ 列最大值要求的位置

#### 情况 2：v 只出现在 $X$ 中

- **约束**：只能填写在位置 $(i, j)$，其中：
  - $X[i] = v$
  - $Y[j] > v$（确保不破坏列约束）
  - 位置未被占用

#### 情况 3：v 只出现在 $Y$ 中

- **约束**：只能填写在位置 $(i, j)$，其中：
  - $Y[j] = v$
  - $X[i] > v$（确保不破坏行约束）
  - 位置未被占用

#### 情况 4：v 不在 $X$ 和 $Y$ 中

- **约束**：只能填写在位置 $(i, j)$，其中：
  - $X[i] > v$ 且 $Y[j] > v$
  - 位置未被占用

## 代码

```c++
#include <bits/stdc++.h>

using i64 = long long;
void go() {
  int n, m;
  std::cin >> n >> m;
  std::vector<int> x(n), y(m);
  for (int i = 0; i < n; i++) {
    std::cin >> x[i];
    x[i]--;
  }
  for (int i = 0; i < m; i++) {
    std::cin >> y[i];
    y[i]--;
  }
  // 表示某个数字在x,y中的位置
  std::vector<int> gx(n * m, -1), gy(m * n, -1);
  for (int i = 0; i < n; i++) {
    if (gx[x[i]] != -1) {
      std::cout << "No" << '\n';
      return;
    }
    gx[x[i]] = i;
  }
  for (int i = 0; i < m; i++) {
    if (gy[y[i]] != -1) {
      std::cout << "No" << '\n';
      return;
    }
    gy[y[i]] = i;
  }
  // 表示当前数字v可以填的位置
  std::queue<std::pair<int, int>> q;
  // 表示数字v==std::min(x[i],y[i])的情况下可以填写的位置
  std::vector ok(n * m, std::vector<std::pair<int, int>>{});

  for (int i = 0; i < n; i++) {
    for (int j = 0; j < m; j++) {
      int v = std::min(x[i], y[j]);
      ok[v].push_back({i, j});
    }
  }
  std::vector ans(n, std::vector<int>(m));
  for (int v = n * m - 1; v >= 0; v--) {
    // v不在x中而且v不在y中
    if (gx[v] == -1 && gy[v] == -1) {
      if (q.empty()) {
        std::cout << "No" << '\n';
        return;
      }
      auto [i, j] = q.front();
      q.pop();
      ans[i][j] = v + 1;
      // 现在可以拿来放v位置就拿去放比v更小的数字
      for (auto &[ii, jj] : ok[v]) {
        q.push({ii, jj});
      }
    }
    // v只在x或y中
    else if (gx[v] == -1 || gy[v] == -1) {
      if (ok[v].empty()) {
        std::cout << "No" << '\n';
        return;
      }
      auto [i, j] = ok[v].back();
      ok[v].pop_back();
      ans[i][j] = v + 1;
      // TODO:
      for (auto &[ii, jj] : ok[v]) {
        q.push({ii, jj});
      }
    } // v在x和y中
    else {
      int i = gx[v];
      int j = gy[v];
      ans[i][j] = v + 1;
      for (auto [ii, jj] : ok[v]) {
        if (i != ii || j != jj)
          q.push({ii, jj});
      }
    }
  }

  std::cout << "Yes" << '\n';
  for (int i = 0; i < n; i++) {
    for (int j = 0; j < m; j++) {
      std::cout << ans[i][j] << " \n"[j == m - 1];
    }
  }
}

int main() {
  std::ios::sync_with_stdio(false);
  std::cin.tie(nullptr);
  int t;
  std::cin >> t;
  while (t--)
    go();
  return 0;
}

```


