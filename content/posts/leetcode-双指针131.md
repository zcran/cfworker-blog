---
title: "leetcode-双指针131"
date: 2026-09-05T09:53:09+08:00
tags: ["leetcode", "双指针"]
draft: false
---


## 限制有序数组中的元素出现次数

给你一个 按升序排序 的整数数组 nums 和一个整数 k。

返回一个数组，使得每个 不同 元素最多出现 k 次，同时保持 nums 中元素的相对顺序不变。


```
impl Solution {
    pub fn limit_occurrences(mut nums: Vec<i32>, k: i32) -> Vec<i32> {
        let k = k as usize;
        let mut size = k.min(nums.len()); // 前 k 个元素默认保留

        // 从第 k 个元素开始，只需与结果中倒数第 k 个元素比较
        for i in k..nums.len() {
            if nums[i] != nums[size - k] {
                nums[size] = nums[i];
                size += 1;
            }
        }

        nums.truncate(size);
        nums
    }
}
```
