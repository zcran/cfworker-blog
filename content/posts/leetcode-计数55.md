---
title: "leetcode-计数55"
date: 2026-08-01T10:40:53+08:00
tags: ["leetcode", "计数"]
draft: false
---


## 元素计数

给你一个整数数组 nums ，统计并返回在 nums 中同时至少具有一个严格较小元素和一个严格较大元素的元素数目。




```
impl Solution {
    pub fn count_elements(mut nums: Vec<i32>) -> i32 {
        // 排序后，只有介于最小值和最大值之间的元素才满足条件
        nums.sort_unstable();

        // 找到第一个不等于最小值的下标
        let left = nums.iter().position(|&x| x != nums[0]);
        // 找到最后一个不等于最大值的下标（从右往左）
        let right = nums.iter().rposition(|&x| x != *nums.last().unwrap());

        match (left, right) {
            (Some(l), Some(r)) if l <= r => (r - l + 1) as i32,
            _ => 0,
        }
    }
}
```
