---
title: "leetcode-前缀和84"
date: 2026-08-25T10:02:51+08:00
tags: ["leetcode", "前缀和"]
draft: false
---


## 一个数组所有前缀的分数

定义一个数组 arr 的 转换数组 conver 为：

conver[i] = arr[i] + max(arr[0..i])，其中 max(arr[0..i]) 是满足 0 <= j <= i 的所有 arr[j] 中的最大值。

定义一个数组 arr 的 分数 为 arr 转换数组中所有元素的和。

给你一个下标从 0 开始长度为 n 的整数数组 nums ，请你返回一个长度为 n 的数组 ans ，其中 ans[i]是前缀 nums[0..i] 的分数。


```
impl Solution {
    pub fn find_prefix_score(nums: Vec<i32>) -> Vec<i64> {
        let mut prefix_sum = 0i64;
        let mut max_val = 0i64;

        nums.iter()
            .map(|&num| {
                let num = num as i64;
                max_val = max_val.max(num);
                let score = num + max_val + prefix_sum;
                prefix_sum = score;
                score
            })
            .collect()
    }
}
```
