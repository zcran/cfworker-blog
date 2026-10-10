---
title: "leetcode-数组29"
date: 2026-10-10T20:38:42+08:00
tags: ["leetcode", "数组"]
draft: false
---


## 删除一个元素使数组严格递增

给你一个下标从 0 开始的整数数组 nums ，如果 恰好 删除 一个 元素后，数组 严格递增 ，那么请你返回 true ，否则返回 false 。如果数组本身已经是严格递增的，请你也返回 true 。

数组 nums 是 严格递增 的定义为：对于任意下标的 1 <= i < nums.length 都满足 nums[i - 1] < nums[i] 。


```
impl Solution {
    /// 判断能否最多删除一个元素使数组严格递增。
    ///
    /// 遍历找到第一处下降 `nums[i] <= nums[i-1]`，此时必须删除
    /// `nums[i-1]` 或 `nums[i]`：
    /// - 若 `i == 1` 或 `nums[i-2] < nums[i]`，删除 `nums[i-1]`；
    /// - 否则若 `i == n-1` 或 `nums[i-1] < nums[i+1]`，删除 `nums[i]`；
    /// - 两者都不行则返回 false。
    /// 若出现第二处下降，直接返回 false。
    ///
    /// 时间复杂度 O(n)，空间复杂度 O(1)。
    pub fn can_be_increasing(nums: Vec<i32>) -> bool {
        let n = nums.len();
        let mut removed = false;

        for i in 1..n {
            if nums[i] <= nums[i - 1] {
                if removed {
                    return false;
                }
                removed = true;

                // 删除 nums[i-1]：需 i == 1 或 nums[i-2] < nums[i]
                // 删除 nums[i]：需 i == n-1 或 nums[i-1] < nums[i+1]
                let can_remove_prev = i == 1 || nums[i - 2] < nums[i];
                let can_remove_cur = i == n - 1 || nums[i - 1] < nums[i + 1];

                if !can_remove_prev && !can_remove_cur {
                    return false;
                }
            }
        }

        true
    }
}
```
