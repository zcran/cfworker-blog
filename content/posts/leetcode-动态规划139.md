---
title: "leetcode-动态规划139"
date: 2026-10-05T20:40:03+08:00
tags: ["leetcode", "动态规划"]
draft: false
---


## 最高乘法得分

给你一个大小为 4 的整数数组 a 和一个大小 至少为 4 的整数数组 b。

你需要从数组 b 中选择四个下标 i0, i1, i2, 和 i3，并满足 i0 < i1 < i2 < i3。你的得分将是 a[0] * b[i0] + a[1] * b[i1] + a[2] * b[i2] + a[3] * b[i3] 的值。

返回你能够获得的 最大 得分。


```
impl Solution {
    pub fn max_score(a: Vec<i32>, b: Vec<i32>) -> i64 {
        const NEG_INF: i64 = i64::MIN / 2;

        // dp[k]：已为 a[0..k-1] 选好下标时的最大得分
        let mut dp = [NEG_INF; 5];
        dp[0] = 0;

        for x in b {
            let x = x as i64;
            // 倒序更新，保证每个 b 元素至多被使用一次
            for k in (0..4).rev() {
                dp[k + 1] = dp[k + 1].max(dp[k] + a[k] as i64 * x);
            }
        }

        dp[4]
    }
}
```
