---
title: "leetcode-枚举48"
date: 2026-07-09T10:05:01+08:00
tags: ["leetcode", "枚举"]
draft: false
---


## 给小朋友们分糖果 II

给你两个正整数 n 和 limit 。

请你将 n 颗糖果分给 3 位小朋友，确保没有任何小朋友得到超过 limit 颗糖果，请你返回满足此条件下的 总方案数 。


```
impl Solution {
    pub fn distribute_candies(n: i32, limit: i32) -> i64 {
        let mut ans = 0i64;

        // 枚举第一个小朋友得到的糖果数 i
        for i in 0..=limit.min(n) {
            let remaining = n - i; // 剩余给第二个和第三个小朋友的糖果

            // 如果剩余糖果超过 2*limit，即使两个小朋友都拿 limit 也不够，跳过
            if remaining > 2 * limit {
                continue;
            }

            // 第二个小朋友能拿的糖果范围：
            // 下限：至少要让第三个小朋友不超过 limit，即 remaining - limit
            // 上限：不能超过 limit，也不能超过剩余总数
            let min_j = 0.max(remaining - limit);
            let max_j = limit.min(remaining);

            // 第二个小朋友的可行取值数量
            ans += (max_j - min_j + 1) as i64;
        }

        ans
    }
}
```
