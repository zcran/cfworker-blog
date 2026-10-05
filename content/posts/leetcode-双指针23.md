---
title: "leetcode-双指针23"
date: 2026-09-05T09:53:02+08:00
tags: ["leetcode", "双指针"]
draft: false
---


## 两个数组的交集 II

给你两个整数数组 nums1 和 nums2 ，请你以数组形式返回两数组的交集。返回结果中每个元素出现的次数，应与元素在两个数组中都出现的次数一致（如果出现次数不一致，则考虑取较小值）。可以不考虑输出结果的顺序。


```
use std::collections::HashMap;

impl Solution {
    pub fn intersect(nums1: Vec<i32>, nums2: Vec<i32>) -> Vec<i32> {
        // 用较短的数组构建哈希表，减少内存占用
        let (mut short, long) = if nums1.len() <= nums2.len() {
            (nums1, nums2)
        } else {
            (nums2, nums1)
        };

        // 统计短数组中每个元素的出现次数
        let mut counts = HashMap::with_capacity(short.len());
        for &num in &short {
            *counts.entry(num).or_insert(0) += 1;
        }

        // 遍历长数组，收集存在于哈希表中的元素
        let mut result = Vec::with_capacity(short.len());
        for num in long {
            if let Some(count) = counts.get_mut(&num) {
                if *count > 0 {
                    result.push(num);
                    *count -= 1;
                }
            }
        }

        result
    }
}
```
