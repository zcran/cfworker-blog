---
title: "leetcode-二分查找98"
date: 2026-09-28T12:29:17+08:00
tags: ["leetcode", "二分查找"]
draft: false
---


## 有序数组中的单一元素

给定一个只包含整数的有序数组 nums ，每个元素都会出现两次，唯有一个数只会出现一次，请找出这个唯一的数字。


```
impl Solution {
    pub fn single_non_duplicate(nums: Vec<i32>) -> i32 {
        let (mut lo, mut hi) = (0, nums.len() - 1);
        while lo < hi {
            let mid = lo + (hi - lo) / 2;
            // 利用成对性质：若 nums[mid] == nums[mid ^ 1]，说明唯一的数在 mid 右侧
            if nums[mid] == nums[mid ^ 1] {
                lo = mid + 1;
            } else {
                hi = mid;
            }
        }
        nums[lo]
    }
}
```
