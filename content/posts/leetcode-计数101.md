---
title: "leetcode-计数101"
date: 2026-08-01T10:40:56+08:00
tags: ["leetcode", "计数"]
draft: false
---


## 构成整天的下标对数目 I

给你一个整数数组 hours，表示以 小时 为单位的时间，返回一个整数，表示满足 i < j 且 hours[i] + hours[j] 构成 整天 的下标对 i, j 的数目。

整天 定义为时间持续时间是 24 小时的 整数倍 。

例如，1 天是 24 小时，2 天是 48 小时，3 天是 72 小时，以此类推。


```
impl Solution {
    pub fn count_complete_day_pairs(hours: Vec<i32>) -> i64 {
        let mut cnt = [0i64; 24];

        // 统计每个余数出现次数
        for &h in &hours {
            cnt[(h % 24) as usize] += 1;
        }

        let mut ans = 0i64;

        // 余数 0 和 12 的：内部配对（需要两个相同余数）
        ans += cnt[0] * (cnt[0] - 1) / 2;
        ans += cnt[12] * (cnt[12] - 1) / 2;

        // 余数 r 和 24-r 配对（避免重复，只遍历 1..12）
        for r in 1..12 {
            ans += cnt[r] * cnt[24 - r];
        }

        ans
    }
}
```
