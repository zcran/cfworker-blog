---
title: "leetcode-计数21"
date: 2026-08-01T10:40:51+08:00
tags: ["leetcode", "计数"]
draft: false
---


## 根据数字二进制下 1 的数目排序

给你一个整数数组 arr 。请你将数组中的元素按照其二进制表示中数字 1 的数目升序排序。

如果存在多个数字二进制中 1 的数目相同，则必须将它们按照数值大小升序排列。

请你返回排序后的数组。




```
impl Solution {
    pub fn sort_by_bits(mut arr: Vec<i32>) -> Vec<i32> {
        // 计算一个整数中二进制1的个数（使用内置方法）
        let count_ones = |x: i32| x.count_ones();

        // 自定义排序：先按1的个数，再按数值大小
        arr.sort_unstable_by(|&a, &b| {
            let ones_a = count_ones(a);
            let ones_b = count_ones(b);
            ones_a.cmp(&ones_b).then(a.cmp(&b))
        });

        arr
    }
}
```
