---
title: "leetcode-双指针3"
date: 2026-09-05T09:53:00+08:00
tags: ["leetcode", "双指针"]
draft: false
---


## 三数之和

给你一个整数数组 nums ，判断是否存在三元组 [nums[i], nums[j], nums[k]] 满足 i != j、i != k 且 j != k ，同时还满足 nums[i] + nums[j] + nums[k] == 0 。请你返回所有和为 0 且不重复的三元组。

注意：答案中不可以包含重复的三元组。


```
impl Solution {
    /// 三数之和（双指针法）
    /// 时间复杂度 O(n²)，空间复杂度 O(1)（不计返回结果）
    /// 核心：排序 + 固定一个数，双指针扫描剩余区间
    pub fn three_sum(mut nums: Vec<i32>) -> Vec<Vec<i32>> {
        nums.sort_unstable();
        let n = nums.len();
        let mut result = Vec::new();

        for i in 0..n - 2 {
            // 跳过重复的固定元素
            if i > 0 && nums[i] == nums[i - 1] {
                continue;
            }

            let target = -nums[i];
            let mut left = i + 1;
            let mut right = n - 1;

            // 双指针查找两数之和等于 target
            while left < right {
                let sum = nums[left] + nums[right];

                if sum < target {
                    left += 1;
                } else if sum > target {
                    right -= 1;
                } else {
                    // 找到一组解
                    result.push(vec![nums[i], nums[left], nums[right]]);

                    // 跳过重复的左指针元素
                    left += 1;
                    while left < right && nums[left] == nums[left - 1] {
                        left += 1;
                    }

                    // 跳过重复的右指针元素
                    right -= 1;
                    while left < right && nums[right] == nums[right + 1] {
                        right -= 1;
                    }
                }
            }
        }

        result
    }
}
```
