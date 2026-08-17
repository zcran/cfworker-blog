---
title: "leetcode-模拟61"
date: 2026-08-08T11:31:12+08:00
tags: ["leetcode", "模拟"]
draft: false
---


## 按符号重排数组

给你一个下标从 0 开始的整数数组 nums ，数组长度为 偶数 ，由数目 相等 的正整数和负整数组成。

你需要返回满足下述条件的数组 nums：

1. 任意 连续 的两个整数 符号相反
2. 对于符号相同的所有整数，保留 它们在 nums 中的 顺序 。
3. 重排后数组以正整数开头。

重排元素满足上述条件后，返回修改后的数组。


```
impl Solution {
    /// 重排数组：正负交替，正数开头，同号数保持原相对顺序。
    pub fn rearrange_array(nums: Vec<i32>) -> Vec<i32> {
        let (pos, neg): (Vec<i32>, Vec<i32>) = nums.into_iter().partition(|&x| x > 0);
        let mut ans = Vec::with_capacity(pos.len() * 2);

        for (p, n) in pos.into_iter().zip(neg) {
            ans.extend([p, n]);
        }
        ans
    }
}
```
