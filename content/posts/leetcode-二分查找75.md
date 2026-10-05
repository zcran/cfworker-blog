---
title: "leetcode-二分查找75"
date: 2026-09-28T12:29:15+08:00
tags: ["leetcode", "二分查找"]
draft: false
---


## 检测相邻递增子数组 II

给你一个由 n 个整数组成的数组 nums ，请你找出 k 的 最大值，使得存在 两个 相邻 且长度为 k 的 严格递增 子数组。具体来说，需要检查是否存在从下标 a 和 b (a < b) 开始的 两个 子数组，并满足下述全部条件：

这两个子数组 nums[a..a + k - 1] 和 nums[b..b + k - 1] 都是 严格递增 的。
这两个子数组必须是 相邻的，即 b = a + k。

返回 k 的 最大可能 值。

子数组 是数组中的一个连续 非空 的元素序列。


```
impl Solution {
    pub fn max_increasing_subarrays(nums: Vec<i32>) -> i32 {
        let n = nums.len();
        let mut cur = 1;      // 当前严格递增连续段的长度
        let mut prev = 0;     // 上一个严格递增连续段的长度
        let mut ans = 0;

        for i in 1..n {
            if nums[i] > nums[i - 1] {
                cur += 1;
            } else {
                prev = cur;
                cur = 1;
            }
            // 两个相邻子数组分别来自 prev 和 cur，最多各取 min(prev, cur)
            ans = ans.max(prev.min(cur));
            // 同一个长度为 cur 的递增段可拆成前后两个相邻子数组
            ans = ans.max(cur / 2);
        }

        ans
    }
}
```
