---
title: "leetcode-模拟93"
date: 2026-08-08T11:31:14+08:00
tags: ["leetcode", "模拟"]
draft: false
---


## 半有序排列

给你一个下标从 0 开始、长度为 n 的整数排列 nums 。

如果排列的第一个数字等于 1 且最后一个数字等于 n ，则称其为 半有序排列 。你可以执行多次下述操作，直到将 nums 变成一个 半有序排列 ：

选择 nums 中相邻的两个元素，然后交换它们。

返回使 nums 变成 半有序排列 所需的最小操作次数。

排列 是一个长度为 n 的整数序列，其中包含从 1 到 n 的每个数字恰好一次。


```
impl Solution {
    /// 返回将排列变为半有序（首元素为1，末元素为n）所需的最小相邻交换次数。
    ///
    /// 思路：
    /// - 1 在位置 idx_1，需左移 idx_1 步到首位；
    /// - n 在位置 idx_n，需右移 (n-1-idx_n) 步到末位；
    /// - 若 n 在 1 左侧（idx_n < idx_1），移动 1 时会"推" n 左移一格，总步数减 1。
    pub fn semi_ordered_permutation(nums: Vec<i32>) -> i32 {
        let n = nums.len();
        let (mut idx_1, mut idx_n) = (0, 0);

        for (i, &x) in nums.iter().enumerate() {
            if x == 1 {
                idx_1 = i;
            } else if x == n as i32 {
                idx_n = i;
            }
        }

        let overlap = (idx_n < idx_1) as usize;
        (idx_1 + n - 1 - idx_n - overlap) as i32
    }
}
```
