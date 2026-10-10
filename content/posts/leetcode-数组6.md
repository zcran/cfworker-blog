---
title: "leetcode-数组6"
date: 2026-10-10T20:38:40+08:00
tags: ["leetcode", "数组"]
draft: false
---


## 非递减数列

给你一个长度为 n 的整数数组 nums ，请你判断在 最多 改变 1 个元素的情况下，该数组能否变成一个非递减数列。

我们是这样定义一个非递减数列的： 对于数组中任意的 i (0 <= i <= n-2)，总满足 nums[i] <= nums[i + 1]。


```
impl Solution {
    /// 判断最多修改 1 个元素能否使数组非递减。
    ///
    /// 遍历时找到第一处下降 `nums[i-1] > nums[i]`，此时必须修改
    /// `nums[i-1]` 或 `nums[i]`：
    /// - 若 `i == 1` 或 `nums[i-2] <= nums[i]`，可把 `nums[i-1]` 调小；
    /// - 否则若 `i == n-1` 或 `nums[i-1] <= nums[i+1]`，可把 `nums[i]` 调大；
    /// - 两者都不行则无法一次修复，返回 false。
    /// 若出现第二处下降，直接返回 false。
    ///
    /// 时间复杂度 O(n)，空间复杂度 O(1)。
    pub fn check_possibility(nums: Vec<i32>) -> bool {
        let n = nums.len();
        let mut modified = false;

        for i in 1..n {
            if nums[i] < nums[i - 1] {
                if modified {
                    return false;
                }
                modified = true;

                // 修改 nums[i-1] 为 nums[i]：需 i == 1 或 nums[i-2] <= nums[i]
                // 修改 nums[i] 为 nums[i-1]：需 i == n-1 或 nums[i-1] <= nums[i+1]
                let can_lower_prev = i == 1 || nums[i - 2] <= nums[i];
                let can_raise_cur = i == n - 1 || nums[i - 1] <= nums[i + 1];

                if !can_lower_prev && !can_raise_cur {
                    return false;
                }
            }
        }

        true
    }
}
```
