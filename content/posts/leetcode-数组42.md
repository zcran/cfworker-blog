---
title: "leetcode-数组42"
date: 2026-10-10T20:38:43+08:00
tags: ["leetcode", "数组"]
draft: false
---


## 使数组成为递增数组的最少右移次数

给你一个长度为 n 下标从 0 开始的数组 nums ，数组中的元素为 互不相同 的正整数。请你返回让 nums 成为递增数组的 最少右移 次数，如果无法得到递增数组，返回 -1 。

一次 右移 指的是同时对所有下标进行操作，将下标为 i 的元素移动到下标 (i + 1) % n 处。


```
impl Solution {
    /// 求使数组递增的最少右移次数，无法做到则返回 -1。
    ///
    /// 右移等价于把末尾若干元素搬到前面。若数组元素互不相同，
    /// 则递增数组最多只能有 1 处下降（即轮转断点）。
    /// 遍历相邻对，统计下降次数及位置：
    /// - 0 处下降：已递增，返回 0；
    /// - 1 处下降且 `nums[0] > nums[n-1]`：断点在 `i` 与 `i+1` 之间，
    ///   需右移 `n - 1 - i` 次；
    /// - 其余情况：无法递增，返回 -1。
    ///
    /// 时间复杂度 O(n)，空间复杂度 O(1)。
    pub fn minimum_right_shifts(nums: Vec<i32>) -> i32 {
        let n = nums.len();
        let mut drops = 0;
        let mut drop_pos = 0;

        for i in 0..n - 1 {
            if nums[i + 1] < nums[i] {
                drops += 1;
                drop_pos = i;
            }
        }

        match drops {
            0 => 0,
            1 if nums[0] > nums[n - 1] => (n - 1 - drop_pos) as i32,
            _ => -1,
        }
    }
}
```
