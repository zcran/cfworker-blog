---
title: "leetcode-前缀和54"
date: 2026-08-25T10:02:49+08:00
tags: ["leetcode", "前缀和"]
draft: false
---


## 适合野炊的日子

你和朋友们准备去野炊。给你一个下标从 0 开始的整数数组 security ，其中 security[i] 是第 i 天的建议出行指数。日子从 0 开始编号。同时给你一个整数 time 。

如果第 i 天满足以下所有条件，我们称它为一个适合野炊的日子：

第 i 天前和后都分别至少有 time 天。
第 i 天前连续 time 天建议出行指数都是非递增的。
第 i 天后连续 time 天建议出行指数都是非递减的。

更正式的，第 i 天是一个适合野炊的日子当且仅当：security[i - time] >= security[i - time + 1] >= ... >= security[i] <= ... <= security[i + time - 1] <= security[i + time].

请你返回一个数组，包含 所有 适合野炊的日子（下标从 0 开始）。返回的日子可以 任意 顺序排列。


```
impl Solution {
    pub fn good_days_to_rob_bank(security: Vec<i32>, time: i32) -> Vec<i32> {
        let n = security.len();
        let time = time as usize;

        // 如果 time 为 0，每一天都是好日子
        if time == 0 {
            return (0..n as i32).collect();
        }

        // 如果数组长度不足以容纳 time 天前后，直接返回空
        if n < 2 * time + 1 {
            return Vec::new();
        }

        // non_increasing[i] = 从 i 开始向左连续非递增的长度（包含 i）
        let mut non_increasing = vec![0; n];
        // non_decreasing[i] = 从 i 开始向右连续非递减的长度（包含 i）
        let mut non_decreasing = vec![0; n];

        // 初始化：每个位置至少长度为 1
        non_increasing[0] = 1;
        non_decreasing[n - 1] = 1;

        // 从左到右计算非递增长度
        for i in 1..n {
            if security[i - 1] >= security[i] {
                non_increasing[i] = non_increasing[i - 1] + 1;
            } else {
                non_increasing[i] = 1;
            }
        }

        // 从右到左计算非递减长度
        for i in (0..n - 1).rev() {
            if security[i] <= security[i + 1] {
                non_decreasing[i] = non_decreasing[i + 1] + 1;
            } else {
                non_decreasing[i] = 1;
            }
        }

        // 收集所有满足条件的好日子
        let mut ans = Vec::new();
        for i in time..n - time {
            // 左边至少有 time 天非递增，右边至少有 time 天非递减
            // 由于 non_increasing[i] 包含自身，需要 > time 表示至少有 time 天在左边
            if non_increasing[i] > time && non_decreasing[i] > time {
                ans.push(i as i32);
            }
        }

        ans
    }
}
```
