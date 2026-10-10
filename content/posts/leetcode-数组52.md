---
title: "leetcode-数组52"
date: 2026-10-10T20:38:44+08:00
tags: ["leetcode", "数组"]
draft: false
---


## 检测相邻递增子数组 I

给你一个由 n 个整数组成的数组 nums 和一个整数 k，请你确定是否存在 两个 相邻 且长度为 k 的 严格递增 子数组。具体来说，需要检查是否存在从下标 a 和 b (a < b) 开始的 两个 子数组，并满足下述全部条件：

这两个子数组 nums[a..a + k - 1] 和 nums[b..b + k - 1] 都是 严格递增 的。
这两个子数组必须是 相邻的，即 b = a + k。

如果可以找到这样的 两个 子数组，请返回 true；否则返回 false。

子数组 是数组中的一个连续 非空 的元素序列。


```
impl Solution {
    /// 判断是否存在两个相邻且长度均为 k 的严格递增子数组。
    ///
    /// 将数组按「连续严格递增段」切分。相邻两个长度分别为 `a`、`b` 的
    /// 递增段（中间被一个下降隔开）最多能提供 `min(a, b)` 长度的相邻子数组；
    /// 单个递增段长度 `c` 内部可拆出两个相邻子数组，最多 `c / 2`。
    /// 遍历时维护当前段长 `cur` 与上一段长 `prev`，取上述最大值。
    ///
    /// 时间复杂度 O(n)，空间复杂度 O(1)。
    pub fn has_increasing_subarrays(nums: Vec<i32>, k: i32) -> bool {
        let mut best = 0;
        let mut cur = 1;  // 当前严格递增段长度
        let mut prev = 0; // 上一个严格递增段长度

        for i in 1..nums.len() {
            if nums[i] > nums[i - 1] {
                cur += 1;
            } else {
                prev = cur;
                cur = 1;
            }
            // 相邻两段：min(prev, cur)；单段内部：cur / 2
            best = best.max(prev.min(cur)).max(cur / 2);
        }

        best >= k
    }
}
```
