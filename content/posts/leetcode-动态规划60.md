---
title: "leetcode-动态规划60"
date: 2026-10-05T20:39:58+08:00
tags: ["leetcode", "动态规划"]
draft: false
---


## 最佳观光组合

给你一个正整数数组 values，其中 values[i] 表示第 i 个观光景点的评分，并且两个景点 i 和 j 之间的 距离 为 j - i。

一对景点（i < j）组成的观光组合的得分为 values[i] + values[j] + i - j ，也就是景点的评分之和 减去 它们两者之间的距离。

返回一对观光景点能取得的最高分。


```
impl Solution {
    /// 1014. 最佳观光组合
    ///
    /// 得分为 values[i] + values[j] + i - j（i < j），可重写为
    ///   (values[i] + i) + (values[j] - j)
    /// 因此对每个 j，只需在它左边维护 values[i] + i 的最大值 mx。
    ///
    /// 一次遍历，O(n) 时间、O(1) 空间。初值 mx = values[0] + 0。
    pub fn max_score_sightseeing_pair(values: Vec<i32>) -> i32 {
        let mut ans = 0;
        let mut mx = values[0]; // values[0] + 0
        for j in 1..values.len() {
            let v = values[j];
            ans = ans.max(mx + v - j as i32); // 用左侧最大的 values[i]+i 配对
            mx = mx.max(v + j as i32);        // 更新左侧最大值
        }
        ans
    }
}
```
