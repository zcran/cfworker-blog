---
title: "leetcode-单调栈12"
date: 2026-06-07T10:14:04+08:00
tags: ["leetcode", "单调栈"]
draft: false
---


## 每日温度

给定一个整数数组 temperatures ，表示每天的温度，返回一个数组 answer ，其中 answer[i] 是指对于第 i 天，下一个更高温度出现在几天后。如果气温在这之后都不会升高，请在该位置用 0 来代替。

```
impl Solution {
    /// 每日温度 - 下一个更高温度的天数差
    ///
    /// 核心思路：单调递减栈存储未找到更高温度的下标
    /// - 栈中温度值从底到顶严格递减
    /// - 遇到更高温度时，弹出栈顶并计算天数差
    pub fn daily_temperatures(temperatures: Vec<i32>) -> Vec<i32> {
        let n = temperatures.len();
        let mut result = vec![0; n];           // 预分配结果数组
        let mut stack = Vec::with_capacity(n);  // 单调递减栈，存储下标

        for (i, &temp) in temperatures.iter().enumerate() {
            // 当前温度高于栈顶温度时，找到了栈顶的下一个更高温度
            while let Some(&top) = stack.last() {
                if temperatures[top] >= temp {
                    break;  // 保持单调递减
                }
                // 栈顶温度 < 当前温度，计算天数差
                result[top] = (i - top) as i32;
                stack.pop();
            }
            stack.push(i);
        }

        result  // 栈中剩余元素保持默认值 0
    }
}
```
