---
title: "leetcode-模拟140"
date: 2026-08-08T11:31:17+08:00
tags: ["leetcode", "模拟"]
draft: false
---


## 连接逆序数组

给你一个长度为 n 的整数数组 nums。

构造一个新的长度为 2 * n 的数组 ans，其中前 n 个元素与 nums 相同，后 n 个元素为 nums 的逆序。

具体而言，对于 0 <= i <= n - 1：

ans[i] = nums[i]
ans[i + n] = nums[n - i - 1]

返回整数数组 ans。


```
impl Solution {
    /// 构造 nums 与 nums 逆序拼接的数组
    ///
    /// 单次遍历同时填充对称位置：ans[i] = nums[i], ans[2n-1-i] = nums[i]
    pub fn concat_with_reverse(nums: Vec<i32>) -> Vec<i32> {
        let n = nums.len();
        let mut ans = vec![0; n * 2];

        for i in 0..n {
            ans[i] = nums[i];
            ans[n * 2 - 1 - i] = nums[i];
        }

        ans
    }
}
```
