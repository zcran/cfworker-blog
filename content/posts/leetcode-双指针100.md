---
title: "leetcode-双指针100"
date: 2026-09-05T09:53:07+08:00
tags: ["leetcode", "双指针"]
draft: false
---


## 求出最多标记下标

给你一个下标从 0 开始的整数数组 nums 。

一开始，所有下标都没有被标记。你可以执行以下操作任意次：

选择两个 互不相同且未标记 的下标 i 和 j ，满足 2 * nums[i] <= nums[j] ，标记下标 i 和 j 。

请你执行上述操作任意次，返回 nums 中最多可以标记的下标数目。


```
impl Solution {
    pub fn max_num_of_marked_indices(mut nums: Vec<i32>) -> i32 {
        nums.sort_unstable();
        let n = nums.len();

        // 贪心 + 二分：检查能否标记 2*m 个下标
        // 将最小的 m 个作为左侧（i），最大的 m 个作为右侧（j）进行配对
        let check = |m: usize| -> bool {
            (0..m).all(|i| nums[i] * 2 <= nums[n - m + i])
        };

        let (mut lo, mut hi) = (0, n / 2);
        while lo < hi {
            let mid = lo + (hi - lo + 1) / 2;
            if check(mid) {
                lo = mid;
            } else {
                hi = mid - 1;
            }
        }

        (lo * 2) as i32
    }
}
```
