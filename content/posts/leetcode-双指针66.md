---
title: "leetcode-双指针66"
date: 2026-09-05T09:53:04+08:00
tags: ["leetcode", "双指针"]
draft: false
---


## 两个数组间的距离值

给你两个整数数组 arr1 ， arr2 和一个整数 d ，请你返回两个数组之间的 距离值 。

「距离值」 定义为符合此距离要求的元素数目：对于元素 arr1[i] ，不存在任何元素 arr2[j] 满足 |arr1[i]-arr2[j]| <= d 。


```
impl Solution {
    pub fn find_the_distance_value(arr1: Vec<i32>, mut arr2: Vec<i32>, d: i32) -> i32 {
        // 排序 arr2 以便二分查找
        arr2.sort_unstable();

        arr1.into_iter()
            .filter(|&x| {
                // 二分查找第一个 >= x - d 的位置
                let idx = arr2.partition_point(|&y| y < x - d);
                // 如果 idx 超出范围，或者该位置的元素 > x + d
                // 则说明没有任何 arr2 元素在 [x-d, x+d] 范围内
                idx == arr2.len() || arr2[idx] > x + d
            })
            .count() as i32
    }
}
```
