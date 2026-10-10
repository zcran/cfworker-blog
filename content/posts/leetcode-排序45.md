---
title: "leetcode-排序45"
date: 2026-10-05T00:35:02+08:00
tags: ["leetcode", "排序"]
draft: false
---


## 使数组元素相等的减少操作次数

给你一个整数数组 nums ，你的目标是令 nums 中的所有元素相等。完成一次减少操作需要遵照下面的几个步骤：

1. 找出 nums 中的 最大 值。记这个值为 largest 并取其下标 i （下标从 0 开始计数）。如果有多个元素都是最大值，则取最小的 i 。
2. 找出 nums 中的 下一个最大 值，这个值 严格小于 largest ，记为 nextLargest 。
3. 将 nums[i] 减少到 nextLargest 。

返回使 nums 中的所有元素相等的操作次数。


```
impl Solution {
    /// 返回使 nums 中所有元素相等的操作次数。
    ///
    /// 思路：
    /// 每次操作会把当前最大值降到下一个严格更小的值，
    /// 因此每个不同层级的值都需要被降一次。
    /// 从大到小排序后，若当前元素与下一个元素不同，
    /// 则当前元素及所有比它大的元素都需要再执行一次操作。
    pub fn reduction_operations(mut nums: Vec<i32>) -> i32 {
        // 从大到小排序，便于逐层统计
        nums.sort_unstable_by(|a, b| b.cmp(a));

        nums.windows(2)
            .enumerate()
            .filter(|(_, w)| w[0] != w[1])
            // pos + 1 表示当前层级及以上的元素数量
            .map(|(pos, _)| pos as i32 + 1)
            .sum()
    }
}
```
