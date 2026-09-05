---
title: "leetcode-前缀和108"
date: 2026-08-25T10:02:52+08:00
tags: ["leetcode", "前缀和"]
draft: false
---


## 两个字符串的切换距离

给你两个长度相同的字符串 s 和 t ，以及两个整数数组 nextCost 和 previousCost 。

一次操作中，你可以选择 s 中的一个下标 i ，执行以下操作 之一 ：

将 s[i] 切换为字母表中的下一个字母，如果 s[i] == 'z' ，切换后得到 'a' 。操作的代价为 nextCost[j] ，其中 j 表示 s[i] 在字母表中的下标。

将 s[i] 切换为字母表中的上一个字母，如果 s[i] == 'a' ，切换后得到 'z' 。操作的代价为 previousCost[j] ，其中 j 是 s[i] 在字母表中的下标。

切换距离 指的是将字符串 s 变为字符串 t 的 最少 操作代价总和。

请你返回从 s 到 t 的 切换距离 。


```
impl Solution {
    pub fn shift_distance(s: String, t: String, next_cost: Vec<i32>, previous_cost: Vec<i32>) -> i64 {
        const SIGMA: usize = 26;
        let s = s.as_bytes();
        let t = t.as_bytes();

        // 构建环上前缀和，扩展一倍方便处理循环
        let mut next_prefix = vec![0i64; 2 * SIGMA + 1];
        let mut prev_prefix = vec![0i64; 2 * SIGMA + 1];
        for i in 0..2 * SIGMA {
            next_prefix[i + 1] = next_prefix[i] + next_cost[i % SIGMA] as i64;
            prev_prefix[i + 1] = prev_prefix[i] + previous_cost[i % SIGMA] as i64;
        }

        let mut ans = 0i64;
        for (&sc, &tc) in s.iter().zip(t.iter()) {
            let x = (sc - b'a') as usize;
            let y = (tc - b'a') as usize;

            // 正向距离：从 x 向右走到 y（可能绕一圈）
            let forward = if y >= x {
                next_prefix[y] - next_prefix[x]
            } else {
                next_prefix[y + SIGMA] - next_prefix[x]
            };

            // 反向距离：从 x 向左走到 y（可能绕一圈）
            let backward = if x >= y {
                prev_prefix[x + 1] - prev_prefix[y + 1]
            } else {
                prev_prefix[x + SIGMA + 1] - prev_prefix[y + 1]
            };

            ans += forward.min(backward);
        }

        ans
    }
}
```
