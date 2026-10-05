---
title: "leetcode-位运算74"
date: 2026-09-24T10:33:39+08:00
tags: ["leetcode", "位运算"]
draft: false
---


## 排序排列

给你一个长度为 n 的整数数组 nums，其中 nums 是范围 [0..n - 1] 内所有数字的一个 排列 。

你可以在满足条件 nums[i] AND nums[j] == k 的情况下交换下标 i 和 j 的元素，其中 AND 表示按位与操作，k 是一个非负整数。

返回可以使数组按 非递减 顺序排序的最大值 k（允许进行任意次这样的交换）。如果 nums 已经是有序的，返回 0。

排列 是数组所有元素的一种重新排列。


```
impl Solution {
    pub fn sort_permutation(nums: Vec<i32>) -> i32 {
        // 对于每个位置 i，若 nums[i] != i，则该元素必须参与交换。
        // 交换要求 nums[i] AND nums[j] == k，因此 k 必须是所有错位元素按位与的子集。
        // 取 k 为这些错位元素的按位与（即最大的可行 k）。
        // 初始化为全 1（-1 的补码表示全 1），最后与 0 取最大值。
        let mut ans = -1i32;
        for (i, &x) in nums.iter().enumerate() {
            if i as i32 != x {
                ans &= x;
            }
        }
        ans.max(0)
    }
}
```
