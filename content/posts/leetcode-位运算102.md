---
title: "leetcode-位运算102"
date: 2026-09-24T10:33:40+08:00
tags: ["leetcode", "位运算"]
draft: false
---


## 消失的数字

数组nums包含从0到n的所有整数，但其中缺了一个。请编写代码找出那个缺失的整数。你有办法在O(n)时间内完成吗？

```
impl Solution {
    pub fn missing_number(nums: Vec<i32>) -> i32 {
        // 数组含 0..n 中除一个数外的所有整数，长度为 n，缺失值范围 [0, n]。
        // 把下标 i 对应期望值 i+1（即 1..=n）与数组中所有元素一起异或：
        //   出现两次的数相互抵消，剩下的就是缺失值。
        nums.iter()
            .enumerate()
            .fold(0, |acc, (i, &x)| acc ^ (i as i32 + 1) ^ x)
    }
}
```
