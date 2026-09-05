---
title: "leetcode-前缀和116"
date: 2026-08-25T10:02:53+08:00
tags: ["leetcode", "前缀和"]
draft: false
---


## 合并得到最小旅行时间

给你一个长度为 l 公里的直路，一个整数 n，一个整数 k 和 两个 长度为 n 的整数数组 position 和 time 。

数组 position 列出了路标的位置（单位：公里），并且是 严格 升序排列的（其中 position[0] = 0 且 position[n - 1] = l）。

每个 time[i] 表示从 position[i] 到 position[i + 1] 之间行驶 1 公里所需的时间（单位：分钟）。

你 必须 执行 恰好 k 次合并操作。在一次合并中，你可以选择两个相邻的路标，下标为 i 和 i + 1（其中 i > 0 且 i + 1 < n），并且：

更新索引为 i + 1 的路标，使其时间变为 time[i] + time[i + 1]。

删除索引为 i 的路标。

返回经过 恰好 k 次合并后从 0 到 l 的 最小总旅行时间（单位：分钟）。


```
impl Solution {
    pub fn min_travel_time(l: i32, n: i32, k: i32, position: Vec<i32>, time: Vec<i32>) -> i32 {
        let n = n as usize;
        let k = k as usize;

        // time 的前缀和，只用到前 n-1 个 time
        let mut pref_time = vec![0; n];
        for i in 0..n - 1 {
            pref_time[i + 1] = pref_time[i] + time[i];
        }

        // memo[i][j][left_k]，用 -1 表示未计算
        let mut memo = vec![vec![vec![-1; k + 1]; n]; n];

        fn dfs(
            i: usize,
            j: usize,
            left_k: usize,
            n: usize,
            k: usize,
            position: &[i32],
            pref_time: &[i32],
            memo: &mut Vec<Vec<Vec<i32>>>,
        ) -> i32 {
            if j == n - 1 {
                return if left_k == 0 { 0 } else { i32::MAX / 2 };
            }

            if memo[i][j][left_k] != -1 {
                return memo[i][j][left_k];
            }

            let mut res = i32::MAX;
            let t = pref_time[j + 1] - pref_time[i];

            let max_k = (j + 1 + left_k).min(n - 1);
            for k in j + 1..=max_k {
                let used_merge = k - j - 1;
                let remaining = dfs(
                    j + 1,
                    k,
                    left_k - used_merge,
                    n,
                    k,
                    position,
                    pref_time,
                    memo,
                );
                if remaining < i32::MAX / 2 {
                    let dist = position[k] - position[j];
                    let cost = dist * t + remaining;
                    if cost < res {
                        res = cost;
                    }
                }
            }

            memo[i][j][left_k] = res;
            res
        }

        dfs(0, 0, k, n, k, &position, &pref_time, &mut memo)
    }
}
```
