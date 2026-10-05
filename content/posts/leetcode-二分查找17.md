---
title: "leetcode-二分查找17"
date: 2026-09-28T12:29:11+08:00
tags: ["leetcode", "二分查找"]
draft: false
---


## 有序数组中的单一元素

给你一个仅由整数组成的有序数组，其中每个元素都会出现两次，唯有一个数只会出现一次。

请你找出并返回只出现一次的那个数。

你设计的解决方案必须满足 O(log n) 时间复杂度和 O(1) 空间复杂度。


```
impl Solution {
    pub fn single_non_duplicate(nums: Vec<i32>) -> i32 {
        // 成对比较：在唯一元素之前，nums[2i] == nums[2i+1]；
        // 在唯一元素之后，nums[2i] != nums[2i+1]。
        // 对「是否不等」这一单调性质在 [0, n/2) 上二分。
        let (mut left, mut right) = (0, nums.len() / 2);
        while left < right {
            let mid = left + (right - left) / 2;
            if nums[mid * 2] != nums[mid * 2 + 1] {
                right = mid;
            } else {
                left = mid + 1;
            }
        }
        nums[left * 2]
    }
}
```
