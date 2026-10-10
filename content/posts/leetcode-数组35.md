---
title: "leetcode-数组35"
date: 2026-10-10T20:38:42+08:00
tags: ["leetcode", "数组"]
draft: false
---


## 统计数组中峰和谷的数量

给你一个下标从 0 开始的整数数组 nums 。如果两侧距 i 最近的不相等邻居的值均小于 nums[i] ，则下标 i 是 nums 中，某个峰的一部分。类似地，如果两侧距 i 最近的不相等邻居的值均大于 nums[i] ，则下标 i 是 nums 中某个谷的一部分。对于相邻下标 i 和 j ，如果 nums[i] == nums[j] ， 则认为这两下标属于 同一个 峰或谷。

注意，要使某个下标所做峰或谷的一部分，那么它左右两侧必须 都 存在不相等邻居。

返回 nums 中峰和谷的数量。


```
impl Solution {
    /// 统计数组中的峰和谷数量。
    ///
    /// 峰/谷判定只看「相邻且不相等的元素」构成的变化趋势：
    /// 先将连续相等的元素去重，得到趋势序列 `trend`，
    /// 然后统计 `trend` 中的极值点（局部最大或最小）数量。
    ///
    /// 时间复杂度 O(n)，空间复杂度 O(n)（趋势序列）。
    pub fn count_hill_valley(nums: Vec<i32>) -> i32 {
        // 去重：只保留相邻不相等的元素
        let mut trend = Vec::with_capacity(nums.len());
        for &x in &nums {
            if trend.last() != Some(&x) {
                trend.push(x);
            }
        }

        // 趋势序列中，极值点即峰或谷
        let mut count = 0;
        for i in 1..trend.len().saturating_sub(1) {
            let (a, b, c) = (trend[i - 1], trend[i], trend[i + 1]);
            if (a < b && b > c) || (a > b && b < c) {
                count += 1;
            }
        }

        count
    }
}
```
