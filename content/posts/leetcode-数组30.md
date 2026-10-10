---
title: "leetcode-数组30"
date: 2026-10-10T20:38:42+08:00
tags: ["leetcode", "数组"]
draft: false
---


## 数组美丽值求和

给你一个下标从 0 开始的整数数组 nums 。对于每个下标 i（1 <= i <= nums.length - 2），nums[i] 的 美丽值 等于：

· 2，对于所有 0 <= j < i 且 i < k <= nums.length - 1 ，满足 nums[j] < nums[i] < nums[k]
· 1，如果满足 nums[i - 1] < nums[i] < nums[i + 1] ，且不满足前面的条件
· 0，如果上述条件全部不满足

返回符合 1 <= i <= nums.length - 2 的所有 nums[i] 的 美丽值的总和 。


```
impl Solution {
    /// 求所有内部元素的美丽值总和。
    ///
    /// 美丽值定义：
    /// - 2：`nums[i]` 严格大于左侧所有元素，且严格小于右侧所有元素；
    /// - 1：`nums[i-1] < nums[i] < nums[i+1]`，且不满足 2；
    /// - 0：其余情况。
    ///
    /// 做法：
    /// 1. 从左到右预处理 `state[i]`，标记 `nums[i]` 是否严格大于左侧所有元素；
    /// 2. 从右到左维护右侧最小值 `suf_min`，对每个内部下标判断美丽值。
    ///
    /// 时间复杂度 O(n)，空间复杂度 O(n)（`state` 数组）。
    pub fn sum_of_beauties(nums: Vec<i32>) -> i32 {
        let n = nums.len();
        let mut state = vec![false; n];

        // 预处理：state[i] = nums[i] 是否严格大于 nums[0..i]
        let mut pre_max = nums[0];
        for i in 1..n - 1 {
            if nums[i] > pre_max {
                state[i] = true;
                pre_max = nums[i];
            }
        }

        // 从右向左扫描，维护右侧最小值
        let mut suf_min = nums[n - 1];
        let mut res = 0;
        for i in (1..n - 1).rev() {
            if state[i] && nums[i] < suf_min {
                res += 2;
            } else if nums[i - 1] < nums[i] && nums[i] < nums[i + 1] {
                res += 1;
            }
            suf_min = suf_min.min(nums[i]);
        }

        res
    }
}
```
