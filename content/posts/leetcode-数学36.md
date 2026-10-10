---
title: "leetcode-数学36"
date: 2026-10-07T20:39:30+08:00
tags: ["leetcode", "数学"]
draft: false
---


## 数组元素积的符号

已知函数 signFunc(x) 将会根据 x 的正负返回特定值：

如果 x 是正数，返回 1 。
如果 x 是负数，返回 -1 。
如果 x 是等于 0 ，返回 0 。

给你一个整数数组 nums 。令 product 为数组 nums 中所有元素值的乘积。

返回 signFunc(product) 。


```
impl Solution {
    pub fn array_sign(nums: Vec<i32>) -> i32 {
        // 只需关心符号，不必真的求乘积（会溢出）。
        // 遇到 0 立即返回 0；否则统计负数的个数，偶数个负数为正、奇数个为负。
        let mut sign = 1;
        for num in nums {
            if num == 0 {
                return 0;
            }
            if num < 0 {
                sign = -sign;
            }
        }
        sign
    }
}
```
