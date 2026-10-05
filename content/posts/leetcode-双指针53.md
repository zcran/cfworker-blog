---
title: "leetcode-双指针53"
date: 2026-09-05T09:53:04+08:00
tags: ["leetcode", "双指针"]
draft: false
---


## 按奇偶排序数组

给你一个整数数组 nums，将 nums 中的的所有偶数元素移动到数组的前面，后跟所有奇数元素。

返回满足此条件的 任一数组 作为答案。


```
impl Solution {
    pub fn sort_array_by_parity(mut nums: Vec<i32>) -> Vec<i32> {
        let mut left = 0;
        let mut right = nums.len() - 1;

        while left < right {
            // 如果左指针指向偶数，跳过
            if nums[left] % 2 == 0 {
                left += 1;
            }
            // 如果右指针指向奇数，跳过
            else if nums[right] % 2 == 1 {
                right -= 1;
            }
            // 左奇右偶，交换
            else {
                nums.swap(left, right);
                left += 1;
                right -= 1;
            }
        }

        nums
    }
}
```
