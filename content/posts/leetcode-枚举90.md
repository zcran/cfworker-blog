---
title: "leetcode-枚举90"
date: 2026-07-09T10:05:03+08:00
tags: ["leetcode", "枚举"]
draft: false
---


## 三元素表达式的最大值

给你一个整数数组 nums。

从 nums 中选择三个元素 a、b 和 c，它们的下标需 互不相同 ，使表达式 a + b - c 的值最大化。

返回该表达式可能的 最大值 。


```
impl Solution {
    pub fn maximize_expression_of_three(nums: Vec<i32>) -> i32 {
        // 要最大化 a + b - c，需要：
        // - 选择最大的两个数作为 a 和 b
        // - 选择最小的一个数作为 c
        // 由于三个元素下标互不相同，排序后取最大两个和最小一个即可

        // 用 partial_sort 的思路：只需找最大两个和最小一个，不必完全排序
        let mut nums = nums;
        let n = nums.len();

        // 使用部分选择算法找最大两个和最小一个
        // 找到最小的元素（放到位置0）
        let mut min_idx = 0;
        for i in 1..n {
            if nums[i] < nums[min_idx] {
                min_idx = i;
            }
        }
        nums.swap(0, min_idx);
        let min_val = nums[0];

        // 从剩余元素中找到最大的两个
        // 先找最大值
        let mut max1_idx = 1;
        for i in 2..n {
            if nums[i] > nums[max1_idx] {
                max1_idx = i;
            }
        }
        nums.swap(1, max1_idx);
        let max1 = nums[1];

        // 再找第二大值
        let mut max2_idx = 2;
        for i in 3..n {
            if nums[i] > nums[max2_idx] {
                max2_idx = i;
            }
        }
        let max2 = if n > 2 { nums[max2_idx] } else { nums[1] }; // n>=3 保证

        max1 + max2 - min_val
    }
}
```
