---
title: "leetcode-前缀和1"
date: 2026-08-25T10:02:46+08:00
tags: ["leetcode", "前缀和"]
draft: false
---


## 除了自身以外数组的乘积

给你一个整数数组 nums，返回 数组 answer ，其中 answer[i] 等于 nums 中除了 nums[i] 之外其余各元素的乘积 。

题目数据 保证 数组 nums之中任意元素的全部前缀元素和后缀的乘积都在  32 位 整数范围内。

请 不要使用除法，且在 O(n) 时间复杂度内完成此题。


```
/// 除自身以外数组的乘积
///
/// 核心思路：使用两个方向的前缀乘积，但只用一个数组存储结果
/// 1. 第一遍从左到右：result[i] = nums[0] * nums[1] * ... * nums[i-1]
/// 2. 第二遍从右到左：用变量维护右侧乘积，result[i] *= 右侧乘积
///
/// 时间复杂度 O(n)，空间复杂度 O(1)（不计输出数组）
impl Solution {
    pub fn product_except_self(nums: Vec<i32>) -> Vec<i32> {
        let n = nums.len();
        let mut result = vec![1; n];

        // 第一遍：从左到右，计算每个位置左侧所有元素的乘积
        let mut left_product = 1;
        for i in 0..n {
            result[i] = left_product;
            left_product *= nums[i];
        }

        // 第二遍：从右到左，乘以右侧所有元素的乘积
        let mut right_product = 1;
        for i in (0..n).rev() {
            result[i] *= right_product;
            right_product *= nums[i];
        }

        result
    }
}
```
