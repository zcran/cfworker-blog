---
title: "leetcode-排序29"
date: 2026-10-05T00:35:01+08:00
tags: ["leetcode", "排序"]
draft: false
---


## 有多少小于当前数字的数字

给你一个数组 nums，对于其中每个元素 nums[i]，请你统计数组中比它小的所有数字的数目。

换而言之，对于每个 nums[i] 你必须计算出有效的 j 的数量，其中 j 满足 j != i 且 nums[j] < nums[i] 。

以数组形式返回答案。


```
impl Solution {
    /// 对每个元素统计数组中比它小的元素个数
    ///
    /// 思路：把 (值, 原下标) 排序，顺序扫描即可确定每个值的排名。
    /// 值相同时排名相同（用前一个的答案），否则排名等于当前下标。
    /// 最后按下标回填。
    /// 时间 O(n log n)，额外空间 O(n)。
    pub fn smaller_numbers_than_current(nums: Vec<i32>) -> Vec<i32> {
        let n = nums.len();

        // (值, 原下标) 排序，相同值聚在一起
        let mut sorted: Vec<(i32, usize)> =
            nums.iter().copied().enumerate().map(|(i, v)| (v, i)).collect();
        sorted.sort_unstable();

        let mut ans = vec![0i32; n];
        for i in 0..n {
            // 与前一个值相同则沿用其排名，否则排名恰为 i
            ans[sorted[i].1] = if i > 0 && sorted[i].0 == sorted[i - 1].0 {
                ans[sorted[i - 1].1]
            } else {
                i as i32
            };
        }

        ans
    }
}
```
