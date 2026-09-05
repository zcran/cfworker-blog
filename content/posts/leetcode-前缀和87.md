---
title: "leetcode-前缀和87"
date: 2026-08-25T10:02:51+08:00
tags: ["leetcode", "前缀和"]
draft: false
---


## 使数组中的所有元素都等于零

给你一个下标从 0 开始的整数数组 nums 和一个正整数 k 。

你可以对数组执行下述操作 任意次 ：

从数组中选出长度为 k 的 任一 子数组，并将子数组中每个元素都 减去 1 。

如果你可以使数组中的所有元素都等于 0 ，返回  true ；否则，返回 false 。

子数组 是数组中的一个非空连续元素序列。


```
impl Solution {
    pub fn check_array(mut nums: Vec<i32>, k: i32) -> bool {
        let k = k as usize;
        let n = nums.len();

        // 差分数组思想：cur 表示当前位置受到的前 k-1 个操作的影响
        let mut cur = 0i32;

        for i in 0..n {
            // 减去之前操作对当前位置的累计影响
            nums[i] -= cur;

            // 如果当前元素为负，无法通过后续操作变为0
            if nums[i] < 0 {
                return false;
            }

            // 当前位置需要进行的操作次数
            let ops = nums[i];

            // 更新滑动窗口的总影响
            if i < k - 1 {
                // 窗口未满，直接累加
                cur += ops;
            } else {
                // 窗口已满，移除最早离开窗口的影响
                cur += ops - nums[i - (k - 1)];
            }
        }

        // 最后检查：如果 cur == 0，所有操作影响已消除
        cur == 0
    }
}
```
