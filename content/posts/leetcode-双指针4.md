---
title: "leetcode-双指针4"
date: 2026-09-05T09:53:00+08:00
tags: ["leetcode", "双指针"]
draft: false
---


## 最接近的三数之和

给你一个长度为 n 的整数数组 nums 和 一个目标值 target。请你从 nums 中选出三个在 不同下标位置 的整数，使它们的和与 target 最接近。

返回这三个数的和。

假定每组输入只存在恰好一个解。


```
impl Solution {
    /// 最接近的三数之和（双指针法）
    /// 时间复杂度 O(n²)，空间复杂度 O(1)
    /// 排序后固定一个数，用双指针扫描剩余区间，动态更新最接近的和
    pub fn three_sum_closest(mut nums: Vec<i32>, target: i32) -> i32 {
        nums.sort_unstable();
        let n = nums.len();
        let mut closest = nums[0] + nums[1] + nums[2];

        for i in 0..n - 2 {
            // 跳过重复的固定元素
            if i > 0 && nums[i] == nums[i - 1] {
                continue;
            }

            let mut left = i + 1;
            let mut right = n - 1;

            while left < right {
                let sum = nums[i] + nums[left] + nums[right];

                // 找到精确匹配，直接返回
                if sum == target {
                    return sum;
                }

                // 更新最接近的值
                if (sum - target).abs() < (closest - target).abs() {
                    closest = sum;
                }

                // 根据当前和与目标值的比较，移动指针
                if sum < target {
                    left += 1;
                } else {
                    right -= 1;
                }
            }
        }

        closest
    }
}
```
