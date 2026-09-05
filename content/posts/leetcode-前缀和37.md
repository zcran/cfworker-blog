---
title: "leetcode-前缀和37"
date: 2026-08-25T10:02:48+08:00
tags: ["leetcode", "前缀和"]
draft: false
---


## 找到最高海拔

有一个自行车手打算进行一场公路骑行，这条路线总共由 n + 1 个不同海拔的点组成。自行车手从海拔为 0 的点 0 开始骑行。

给你一个长度为 n 的整数数组 gain ，其中 gain[i] 是点 i 和点 i + 1 的 净海拔高度差（0 <= i < n）。请你返回 最高点的海拔 。


```
impl Solution {
    /// 返回骑行路线中的最高海拔
    ///
    /// 从海拔 0 开始，累加每个海拔差 gain[i]，记录过程中的最大值
    pub fn largest_altitude(gain: Vec<i32>) -> i32 {
        let mut max_alt = 0;
        let mut cur_alt = 0;

        for diff in gain {
            cur_alt += diff;
            max_alt = max_alt.max(cur_alt);
        }

        max_alt
    }
}
```
