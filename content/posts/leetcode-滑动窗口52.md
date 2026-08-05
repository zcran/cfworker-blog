---
title: "leetcode-滑动窗口52"
date: 2026-07-18T11:02:32+08:00
tags: ["leetcode", "滑动窗口"]
draft: false
---


## 股票平滑下跌阶段的数目

给你一个整数数组 prices ，表示一支股票的历史每日股价，其中 prices[i] 是这支股票第 i 天的价格。

一个 平滑下降的阶段 定义为：对于 连续一天或者多天 ，每日股价都比 前一日股价恰好少 1 ，这个阶段第一天的股价没有限制。

请你返回 平滑下降阶段 的数目。


```
impl Solution {
    pub fn get_descent_periods(prices: Vec<i32>) -> i64 {
        let n = prices.len();
        // 空数组没有下降阶段
        if n == 0 {
            return 0;
        }

        // total: 所有平滑下降阶段的总数
        // 初始为1，表示第一个元素本身构成一个长度为1的下降阶段
        let mut total: i64 = 1;

        // current_len: 以当前元素结尾的连续下降阶段长度
        // 对于第一个元素，长度为1
        let mut current_len: i32 = 1;

        // 从第二个元素开始遍历
        for i in 1..n {
            // 如果当前价格比前一天恰好少1，则下降阶段延续
            if prices[i] == prices[i - 1] - 1 {
                current_len += 1;
            } else {
                // 否则中断，重新开始长度为1的阶段
                current_len = 1;
            }

            // 以当前元素结尾的所有下降阶段数量等于 current_len
            // 将这些新增的阶段累加到总数中
            total += current_len as i64;
        }

        total
    }
}
```
