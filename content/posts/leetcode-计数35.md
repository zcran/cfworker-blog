---
title: "leetcode-计数35"
date: 2026-08-01T10:40:52+08:00
tags: ["leetcode", "计数"]
draft: false
---


## 同积元组

给你一个由 不同 正整数组成的数组 nums ，请你返回满足 a * b = c * d 的元组 (a, b, c, d) 的数量。其中 a、b、c 和 d 都是 nums 中的元素，且 a != b != c != d 。


```
impl Solution {
    pub fn tuple_same_product(nums: Vec<i32>) -> i32 {
        use std::collections::HashMap;

        // 统计所有两两乘积出现的次数
        let mut product_count = HashMap::new();
        for i in 0..nums.len() {
            for j in i + 1..nums.len() {
                let product = nums[i] * nums[j];
                *product_count.entry(product).or_insert(0) += 1;
            }
        }

        // 对于每个乘积，从出现次数中计算组合数 C(n, 2) * 8
        // C(n, 2) = n * (n - 1) / 2 对配对
        // 每对配对可以形成 8 种不同的元组排列
        product_count
            .values()
            .filter(|&&count| count > 1)
            .fold(0, |acc, &count| acc + count * (count - 1) / 2 * 8)
    }
}
```
