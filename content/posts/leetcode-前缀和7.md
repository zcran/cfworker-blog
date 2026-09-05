---
title: "leetcode-前缀和7"
date: 2026-08-25T10:02:46+08:00
tags: ["leetcode", "前缀和"]
draft: false
---


## 寻找数组的中心下标

给你一个整数数组 nums ，请计算数组的 中心下标 。

数组 中心下标 是数组的一个下标，其左侧所有元素相加的和等于右侧所有元素相加的和。

如果中心下标位于数组最左端，那么左侧数之和视为 0 ，因为在下标的左侧不存在元素。这一点对于中心下标位于数组最右端同样适用。

如果数组有多个中心下标，应该返回 最靠近左边 的那一个。如果数组不存在中心下标，返回 -1 。


```
/// 寻找数组的中心下标
///
/// 核心思路：前缀和
///
/// 对于位置 i，左侧和为 sum_left，右侧和为 sum_total - sum_left - nums[i]
/// 中心下标满足：sum_left == sum_total - sum_left - nums[i]
/// 即：2 * sum_left + nums[i] == sum_total
///
/// 时间复杂度 O(n)，空间复杂度 O(1)
impl Solution {
    pub fn pivot_index(nums: Vec<i32>) -> i32 {
        let total_sum: i32 = nums.iter().sum();
        let mut left_sum = 0;

        for (i, &num) in nums.iter().enumerate() {
            // 检查当前下标是否为中心下标
            // 左侧和 == 右侧和
            // left_sum == total_sum - left_sum - num
            if left_sum * 2 == total_sum - num {
                return i as i32;
            }
            // 更新左侧和，准备检查下一个位置
            left_sum += num;
        }

        -1
    }
}
```
