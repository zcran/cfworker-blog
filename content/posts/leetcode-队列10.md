---
title: "leetcode-队列10"
date: 2026-05-23T09:21:37+08:00
tags: ["leetcode", "队列"]
draft: false
---


## K 连续位的最小翻转次数


给定一个二进制数组 nums 和一个整数 k 。

k位翻转 就是从 nums 中选择一个长度为 k 的 子数组 ，同时把子数组中的每一个 0 都改成 1 ，把子数组中的每一个 1 都改成 0 。

返回数组中不存在 0 所需的最小 k位翻转 次数。如果不可能，则返回 -1 。

子数组 是数组的 连续 部分。


```
use std::collections::VecDeque;

impl Solution {
    /// 返回将数组全部翻转为 1 所需的最少 K 连续翻转次数，若不可能则返回 -1。
    pub fn min_k_bit_flips(nums: Vec<i32>, k: i32) -> i32 {
        let k = k as usize;
        let n = nums.len();
        let mut flips = 0;
        let mut queue = VecDeque::new(); // 存储翻转区间的起始位置

        for i in 0..n {
            // 移除已超出当前窗口的翻转记录
            while let Some(&front) = queue.front() {
                if front + k <= i {
                    queue.pop_front();
                } else {
                    break;
                }
            }
            // 当前位的实际值 = nums[i] ^ (翻转次数的奇偶性)
            // 实际值为 0 时需要翻转
            if (nums[i] as usize ^ (queue.len() & 1)) == 0 {
                if i + k > n {
                    return -1; // 无法完成翻转
                }
                queue.push_back(i);
                flips += 1;
            }
        }
        flips
    }
}
```
