---
title: "leetcode-贪心159"
date: 2026-10-03T12:55:44+08:00
tags: ["leetcode", "贪心"]
draft: false
---


## 至多 K 个不同元素的最大和


给你一个 正整数 数组 nums 和一个整数 k。

从 nums 中选择最多 k 个元素，使它们的和最大化。但是，所选的数字必须 互不相同 。

返回一个包含所选数字的数组，数组中的元素按 严格递减 顺序排序。


```
impl Solution {
    pub fn max_k_distinct(mut nums: Vec<i32>, k: i32) -> Vec<i32> {
        // 降序排序后去重，前 k 个即为和最大的 k 个互不相同的元素
        nums.sort_unstable_by(|a, b| b.cmp(a));
        nums.dedup();
        nums.truncate(k as usize);
        nums
    }
}
```
