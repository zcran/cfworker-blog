---
title: "leetcode-双指针55"
date: 2026-09-05T09:53:04+08:00
tags: ["leetcode", "双指针"]
draft: false
---


## 按奇偶排序数组 II

给定一个非负整数数组 nums，  nums 中一半整数是 奇数 ，一半整数是 偶数 。

对数组进行排序，以便当 nums[i] 为奇数时，i 也是 奇数 ；当 nums[i] 为偶数时， i 也是 偶数 。

你可以返回 任何满足上述条件的数组作为答案 。


```
impl Solution {
    pub fn sort_array_by_parity_ii(mut nums: Vec<i32>) -> Vec<i32> {
        let mut even = 0; // 偶数位置指针
        let mut odd = 1;  // 奇数位置指针
        let n = nums.len();

        while even < n && odd < n {
            // 找到偶数位置上的奇数
            while even < n && nums[even] % 2 == 0 {
                even += 2;
            }
            // 找到奇数位置上的偶数
            while odd < n && nums[odd] % 2 == 1 {
                odd += 2;
            }
            // 交换，使两个位置都正确
            if even < n && odd < n {
                nums.swap(even, odd);
            }
        }

        nums
    }
}
```
