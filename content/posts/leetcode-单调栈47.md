---
title: "leetcode-单调栈47"
date: 2026-06-07T10:14:05+08:00
tags: ["leetcode", "单调栈"]
draft: false
---


## 将所有元素变为 0 的最少操作次数

给你一个大小为 n 的 非负 整数数组 nums 。你的任务是对该数组执行若干次（可能为 0 次）操作，使得 所有 元素都变为 0。

在一次操作中，你可以选择一个子数组 [i, j]（其中 0 <= i <= j < n），将该子数组中所有 最小的非负整数 的设为 0。

返回使整个数组变为 0 所需的最少操作次数。

一个 子数组 是数组中的一段连续元素。

```
impl Solution {
    /// 最少操作次数
    ///
    /// 策略：每个不同的非零数字需要一次操作
    /// 单调栈用于去重并维护顺序
    pub fn min_operations(nums: Vec<i32>) -> i32 {
        let mut stack = Vec::new();
        let mut ops = 0;

        for &x in &nums {
            // 保持栈严格递增
            while let Some(&top) = stack.last() {
                if top > x {
                    stack.pop();
                } else {
                    break;
                }
            }

            // 遇到零或重复值跳过
            if x == 0 {
                continue;
            }

            // 新数字需要一次操作
            if stack.last().map_or(true, |&top| top < x) {
                ops += 1;
                stack.push(x);
            }
        }

        ops
    }
}
```
