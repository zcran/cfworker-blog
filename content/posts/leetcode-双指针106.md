---
title: "leetcode-双指针106"
date: 2026-09-05T09:53:07+08:00
tags: ["leetcode", "双指针"]
draft: false
---


## 找出满足差值条件的下标 II

给你一个下标从 0 开始、长度为 n 的整数数组 nums ，以及整数 indexDifference 和整数 valueDifference 。

你的任务是从范围 [0, n - 1] 内找出  2 个满足下述所有条件的下标 i 和 j ：

abs(i - j) >= indexDifference 且
abs(nums[i] - nums[j]) >= valueDifference

返回整数数组 answer。如果存在满足题目要求的两个下标，则 answer = [i, j] ；否则，answer = [-1, -1] 。如果存在多组可供选择的下标对，只需要返回其中任意一组即可。

注意：i 和 j 可能 相等 。


```
impl Solution {
    /// 找出满足 |i - j| >= indexDifference 且 |nums[i] - nums[j]| >= valueDifference 的下标对。
    ///
    /// 贪心 + 前缀极值：
    /// 遍历 j，维护前缀 [0, j - d] 中 nums 的最小值和最大值下标。
    /// 只需检查 nums[j] 与该最小/最大值之差是否达标，即可确定是否存在合法 i。
    /// 时间复杂度 O(n)，空间复杂度 O(1)。
    pub fn find_indices(nums: Vec<i32>, index_difference: i32, value_difference: i32) -> Vec<i32> {
        let d = index_difference as usize;
        let v = value_difference;
        let mut min_idx = 0;
        let mut max_idx = 0;

        for j in d..nums.len() {
            let i = j - d; // 新纳入前缀范围的元素下标
            if nums[i] > nums[max_idx] {
                max_idx = i;
            }
            if nums[i] < nums[min_idx] {
                min_idx = i;
            }

            if nums[max_idx] - nums[j] >= v {
                return vec![max_idx as i32, j as i32];
            }
            if nums[j] - nums[min_idx] >= v {
                return vec![min_idx as i32, j as i32];
            }
        }

        vec![-1, -1]
    }
}
```
