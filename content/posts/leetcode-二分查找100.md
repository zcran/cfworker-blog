---
title: "leetcode-二分查找100"
date: 2026-09-28T12:29:17+08:00
tags: ["leetcode", "二分查找"]
draft: false
---


## 魔术索引

魔术索引。 在数组A[0...n-1]中，有所谓的魔术索引，满足条件A[i] = i。给定一个有序整数数组，编写一种方法找出魔术索引，若有的话，在数组A中找出一个魔术索引，如果没有，则返回-1。若有多个魔术索引，返回索引值最小的一个。


```
impl Solution {
    pub fn find_magic_index(nums: Vec<i32>) -> i32 {
        // 数组可能含有重复元素，无法用二分，直接线性扫描找最小的魔术索引
        for (i, &x) in nums.iter().enumerate() {
            if x == i as i32 {
                return i as i32;
            }
        }
        -1
    }
}
```
