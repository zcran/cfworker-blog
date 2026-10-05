---
title: "leetcode-位运算88"
date: 2026-09-24T10:33:39+08:00
tags: ["leetcode", "位运算"]
draft: false
---


## 点名

某班级 n 位同学的学号为 0 ~ n-1。点名结果记录于升序数组 records。假定仅有一位同学缺席，请返回他的学号。


```
impl Solution {
    pub fn take_attendance(nums: Vec<i32>) -> i32 {
        // 学号 0..n-1 中缺一个，数组升序。
        // 二分查找第一个满足 nums[i] != i 的位置，即为缺失的学号。
        // 若全部匹配，则缺的是 n（即 nums.len()）。

        let mut lo = 0i32;
        let mut hi = nums.len() as i32 - 1;

        while lo <= hi {
            let mid = (lo + hi) >> 1;
            if nums[mid as usize] == mid {
                lo = mid + 1; // 缺失位置在右侧
            } else {
                hi = mid - 1; // 缺失位置在左侧或就是 mid
            }
        }

        lo
    }
}
```
