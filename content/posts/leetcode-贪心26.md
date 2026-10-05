---
title: "leetcode-贪心26"
date: 2026-10-03T12:55:35+08:00
tags: ["leetcode", "贪心"]
draft: false
---


## 最小差值 II

给你一个整数数组 nums，和一个整数 k 。

对于每个下标 i（0 <= i < nums.length），将 nums[i] 变成 nums[i] + k 或 nums[i] - k 。

nums 的 分数 是 nums 中最大元素和最小元素的差值。

在更改每个下标对应的值之后，返回 nums 的最小 分数 。


```
impl Solution {
    pub fn smallest_range_ii(mut nums: Vec<i32>, k: i32) -> i32 {
        // 排序后，最优方案一定存在一个分割点 i：
        // 左侧 [0..=i] 全部 +k，右侧 [i+1..] 全部 -k（或全局同向变化）
        nums.sort_unstable();

        let n = nums.len();
        let (min_base, max_base) = (nums[0], nums[n - 1]);
        let mut ans = max_base - min_base;

        for i in 0..n - 1 {
            // 左侧最小值 nums[0] + k，右侧最小值 nums[i+1] - k
            // 左侧最大值 nums[i] + k，  右侧最大值 nums[n-1] - k
            let low  = (min_base + k).min(nums[i + 1] - k);
            let high = (nums[i] + k).max(max_base - k);
            ans = ans.min(high - low);
        }

        ans
    }
}
```
