---
title: "leetcode-二分查找38"
date: 2026-09-28T12:29:12+08:00
tags: ["leetcode", "二分查找"]
draft: false
---


## 第 k 个缺失的正整数

给你一个 严格升序排列 的正整数数组 arr 和一个整数 k 。

请你找到这个数组里第 k 个缺失的正整数。


```
impl Solution {
    /// 返回严格升序正整数数组 arr 中第 k 个缺失的正整数。
    ///
    /// 思路：二分查找。
    ///   对下标 i，arr[i] 之前缺失的正整数个数为 arr[i] - (i + 1) = arr[i] - i - 1。
    ///   该值随 i 单调不减，可二分找第一个满足「缺失数 >= k」的位置 left。
    ///   此时前 left 个元素共缺失 arr[left-1] - left 个（或等价地），
    ///   答案即为 left + k。
    pub fn find_kth_positive(arr: Vec<i32>, k: i32) -> i32 {
        let (mut lo, mut hi) = (0, arr.len());
        while lo < hi {
            let mid = lo + (hi - lo) / 2;
            // arr[mid] 之前缺失的正整数个数
            if arr[mid] - mid as i32 - 1 >= k {
                hi = mid;
            } else {
                lo = mid + 1;
            }
        }
        lo as i32 + k
    }
}
```
