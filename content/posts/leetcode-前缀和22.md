---
title: "leetcode-前缀和22"
date: 2026-08-25T10:02:47+08:00
tags: ["leetcode", "前缀和"]
draft: false
---


## 生成数组

给定三个整数 n、m 和 k 。考虑使用下方描述的算法找出正整数数组中最大的元素。

```
maximum_value = -1
maximum_index = -1
search_cost = 0
n = arr.length
for (i = 0; i < n; i++) {
    if (maximum_value < arr[i]) {
        maximum_value = arr[i]
        maximum_index = i
        search_cost = search_cost + 1
    }
}
return maximum_index
```

请你构建一个具有以下属性的数组 arr ：

arr 中包含确切的 n 个整数。
1 <= arr[i] <= m 其中 (0 <= i < n) 。
将上面提到的算法应用于 arr 之后，search_cost 的值等于 k 。

返回在满足上述条件的情况下构建数组 arr 的 方法数量 ，由于答案可能会很大，所以 必须 对 10^9 + 7 取余。

```
impl Solution {
    pub fn num_of_arrays(n: i32, m: i32, k: i32) -> i32 {
        const MOD: i64 = 1_000_000_007;
        let (n, m, k) = (n as usize, m as usize, k as usize);

        // dp[c][v]：当前长度下，search_cost = c，且最大值为 v 的方案数
        let mut dp = vec![vec![0; m + 1]; k + 1];
        let mut next = vec![vec![0; m + 1]; k + 1];

        // 初始化长度为 1：只有一个元素，最大值为 v，cost 必然为 1
        for v in 1..=m {
            dp[1][v] = 1;
        }

        // 依次增加数组长度
        for _ in 1..n {
            // 清空 next 数组
            for c in 0..=k {
                for v in 0..=m {
                    next[c][v] = 0;
                }
            }

            for c in 1..=k {
                // 计算前缀和 pref[x] = sum_{v=1..x} dp[c][v]
                let mut pref = vec![0; m + 1];
                for v in 1..=m {
                    pref[v] = (pref[v - 1] + dp[c][v]) % MOD;
                }

                for v in 1..=m {
                    // 情况 1：添加一个 ≤ v 的元素，最大值不变，cost 不变
                    // 可选择 1..v 共 v 种
                    next[c][v] = (next[c][v] + dp[c][v] * (v as i64)) % MOD;

                    // 情况 2：添加一个 > v 的元素，新最大值为该元素
                    // 这里处理的是新最大值恰好为 v 的情况，即前一个最大值 < v
                    if c < k {
                        next[c + 1][v] = (next[c + 1][v] + pref[v - 1]) % MOD;
                    }
                }
            }

            std::mem::swap(&mut dp, &mut next);
        }

        // 统计所有可能最大值的方案数，且 cost = k
        let mut ans = 0;
        for v in 1..=m {
            ans = (ans + dp[k][v]) % MOD;
        }
        ans as i32
    }
}
```
