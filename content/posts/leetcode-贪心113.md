---
title: "leetcode-贪心113"
date: 2026-10-03T12:55:41+08:00
tags: ["leetcode", "贪心"]
draft: false
---


## 修改两个元素的最小分数

给你一个下标从 0 开始的整数数组 nums 。

nums 的 最小 得分是满足 0 <= i < j < nums.length 的 |nums[i] - nums[j]| 的最小值。
nums的 最大 得分是满足 0 <= i < j < nums.length 的 |nums[i] - nums[j]| 的最大值。
nums 的分数是 最大 得分与 最小 得分的和。

我们的目标是最小化 nums 的分数。你 最多 可以修改 nums 中 2 个元素的值。

请你返回修改 nums 中 至多两个 元素的值后，可以得到的 最小分数 。

|x| 表示 x 的绝对值。


```
impl Solution {
    pub fn minimize_sum(mut nums: Vec<i32>) -> i32 {
        // 排序后，最小值一定是相邻元素之差，最大值一定是首尾元素之差。
        // 修改至多 2 个元素，等价于从两端各去掉若干元素（共 2 个），
        // 使剩余区间的极差最小。只需比较三种保留方案：
        // 去掉最小的 2 个、去掉最大的 2 个、各去掉 1 个。
        nums.sort_unstable();
        let n = nums.len();
        (nums[n - 3] - nums[0])
            .min(nums[n - 2] - nums[1])
            .min(nums[n - 1] - nums[2])
    }
}
```
