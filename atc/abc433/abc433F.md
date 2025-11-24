---
title: F - 1122 Subsequence 2
---

# F - 1122 Subsequence 2

## 题意

定义一个“1122”串，求数组中有多少 1122 串子序列

## trick

- 1.取出的 1122 串一定是偶数，设长度为 2k,固定住小数的最后一个位置，左右两边可以选择的数字就是 k-1,k 个，为了保持对应关系，我们同时取 i 个。
- 2.可以得到我们要求的柿子    
$$\sum_{i=1}^{\min(L,R)} \binom{L}{i}\binom{R}{i} = \sum_{i=1}^{\min(L,R)} \binom{L}{i}\binom{R}{R-i} = \binom{L+R}{R}$$  
  用到了[范德蒙德卷积](https://oi-wiki.org/math/combinatorics/vandermonde-convolution/)化简
- 3.剩下了利用前缀和 $O(1)$ 求就可以了

## 代码

```c++
#include <bits/stdc++.h>

using i64 = long long;
const int N = 1e6 + 5;
const int mod = 998244353;
i64 f[N], inv[N];
i64 qpow(i64 a, i64 b) {
  i64 ans = 1;
  while (b) {
    if (b & 1) {
      ans = a * ans % mod;
    }
    a = a * a % mod;
    b >>= 1;
  }
  return ans;
}

void go() {
  f[0] = inv[0] = 1;
  for (int i = 1; i < N; i++) {
    f[i] = f[i - 1] * i % mod;
  }
  inv[N - 1] = qpow(f[N - 1], mod - 2);
  for (int i = N - 2; i >= 1; i--) {
    inv[i] = inv[i + 1] * (i + 1) % mod;
  }
  auto C = [&](i64 p, i64 k) { return f[p] * inv[k] % mod * inv[p - k] % mod; };
  std::string s;
  std::cin >> s;
  int n = s.size();
  std::vector a(10, std::vector<i64>(n));
  for (int i = 0; i < n; i++) {
    a[s[i] - '0'][i]++;
  }
  for (int c = 0; c <= 9; c++) {
    for (int i = 1; i < n; i++) {
      a[c][i] = (a[c][i] + a[c][i - 1]) % mod;
    }
  }
  i64 ans = 0;
  for (int i = 0; i < n; i++) {
    if (s[i] == '9')
      continue;
    i64 pre = a[s[i] - '0'][i] - 1;
    i64 suf = (a[s[i] - '0' + 1][n - 1] - a[s[i] - '0' + 1][i] + mod) % mod;
    ans = (ans + C(pre + suf, pre + 1)) % mod;
  }
  std::cout << ans << '\n';
}

int main() {
  std::ios::sync_with_stdio(false);
  std::cin.tie(nullptr);
  go();
  return 0;
}
```

