---
title: "leetcode-数组31"
date: 2026-10-10T20:38:42+08:00
tags: ["leetcode", "数组"]
draft: false
---


## 增量元素之间的最大差值

给你一个下标从 0 开始的整数数组 nums ，该数组的大小为 n ，请你计算 nums[j] - nums[i] 能求得的 最大差值 ，其中 0 <= i < j < n 且 nums[i] < nums[j] 。

返回 最大差值 。如果不存在满足要求的 i 和 j ，返回 -1 。


```
impl Solution {
    /// 求满足 `i < j` 且 `nums[i] < nums[j]` 的最大差值 `nums[j] - nums[i]`。
    ///
    /// 一次遍历，用 `pre_min` 维护当前位置之前的最小值：
    /// - 先把当前元素当作 `nums[j]`，用 `nums[j] - pre_min` 更新答案；
    /// - 再把当前元素当作 `nums[i]`，更新 `pre_min`。
    ///
    /// 时间复杂度 O(n)，空间复杂度 O(1)。
    pub fn maximum_difference(nums: Vec<i32>) -> i32 {
        let mut max_diff = -1;
        let mut pre_min = nums[0];

        for &x in &nums[1..] {
            if x > pre_min {
                max_diff = max_diff.max(x - pre_min);
            } else {
                pre_min = x;
            }
        }

        max_diff
    }
}
```
