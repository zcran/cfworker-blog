---
title: "leetcode-二分查找7"
date: 2026-09-28T12:29:10+08:00
tags: ["leetcode", "二分查找"]
draft: false
---


## 寻找旋转排序数组中的最小值 II

已知一个长度为 n 的数组，预先按照升序排列，经由 1 到 n 次 旋转 后，得到输入数组。例如，原数组 nums = [0,1,4,4,5,6,7] 在变化后可能得到：
若旋转 4 次，则可以得到 [4,5,6,7,0,1,4]
若旋转 7 次，则可以得到 [0,1,4,4,5,6,7]

注意，数组 [a[0], a[1], a[2], ..., a[n-1]] 旋转一次 的结果为数组 [a[n-1], a[0], a[1], a[2], ..., a[n-2]] 。

给你一个可能存在 重复 元素值的数组 nums ，它原来是一个升序排列的数组，并按上述情形进行了多次旋转。请你找出并返回数组中的 最小元素 。

你必须尽可能减少整个过程的操作步骤。


```
impl Solution {
    pub fn find_min(nums: Vec<i32>) -> i32 {
        // 在左闭右开区间 [left, right) 中二分查找最小值
        let (mut left, mut right) = (0, nums.len() - 1);
        while left < right {
            let mid = left + (right - left) / 2;
            match nums[mid].cmp(&nums[right]) {
                // mid 与 right 相等，无法判断，收缩右边界
                std::cmp::Ordering::Equal => right -= 1,
                // 最小值在 [left, mid]
                std::cmp::Ordering::Less => right = mid,
                // 最小值在 [mid+1, right]
                std::cmp::Ordering::Greater => left = mid + 1,
            }
        }
        nums[left]
    }
}
```
