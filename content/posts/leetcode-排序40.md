---
title: "leetcode-排序40"
date: 2026-10-05T00:35:02+08:00
tags: ["leetcode", "排序"]
draft: false
---


## 按照频率将数组升序排序

给你一个整数数组 nums ，请你将数组按照每个值的频率 升序 排序。如果有多个值的频率相同，请你按照数值本身将它们 降序 排序。

请你返回排序后的数组。


```
impl Solution {
    /// 按频率升序排序；频率相同时，数值降序
    ///
    /// 思路：题目值域在 [-100, 100]，用长度 201 的计数数组统计频次，
    /// 再以「频次升序、数值降序」为比较器原地排序。
    /// 计数数组为 O(1) 空间，比较器每步 O(1)。
    /// 时间 O(n log n)，额外空间 O(1)。
    pub fn frequency_sort(mut nums: Vec<i32>) -> Vec<i32> {
        // 频次表：下标偏移 +100 以容纳负值
        let mut cnt = [0u32; 201];
        for &x in &nums {
            cnt[(x + 100) as usize] += 1;
        }

        // 频次升序；频次相同则数值降序
        nums.sort_unstable_by(|&a, &b| {
            let (ca, cb) = (cnt[(a + 100) as usize], cnt[(b + 100) as usize]);
            ca.cmp(&cb).then_with(|| b.cmp(&a))
        });

        nums
    }
}
```
