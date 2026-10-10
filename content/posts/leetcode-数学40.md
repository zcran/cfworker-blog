---
title: "leetcode-数学40"
date: 2026-10-07T20:39:30+08:00
tags: ["leetcode", "数学"]
draft: false
---


## 找到指定长度的回文数

给你一个整数数组 queries 和一个 正 整数 intLength ，请你返回一个数组 answer ，其中 answer[i] 是长度为 intLength 的 正回文数 中第 queries[i] 小的数字，如果不存在这样的回文数，则为 -1 。

回文数 指的是从前往后和从后往前读一模一样的数字。回文数不能有前导 0 。


```
impl Solution {
    pub fn kth_palindrome(queries: Vec<i32>, int_length: i32) -> Vec<i64> {
        let len = int_length as usize;

        // 长度为 len 的正回文数由前 half = (len + 1) / 2 位"种子"决定：
        //   - 种子范围为 [10^(half-1), 10^half - 1]，共 9 * 10^(half-1) 个
        //   - 第 i 个（i 从 1 开始）种子为 10^(half-1) + i - 1
        //   - 若 len 为奇数，镜像时去掉种子最后一位；否则完整镜像
        let half = (len + 1) / 2;
        let limit = 9 * 10i64.pow((half - 1) as u32); // 可用的回文数总数

        queries
            .into_iter()
            .map(|q| {
                let i = q as i64;
                if i > limit {
                    return -1;
                }

                // 构造回文数：种子作为高位，镜像补齐低位
                let mut seed = 10i64.pow((half - 1) as u32) + i - 1;
                let mut ans = seed;

                // 奇数长度时种子的最低位只出现一次，镜像前先去掉
                if len % 2 == 1 {
                    seed /= 10;
                }
                while seed > 0 {
                    ans = ans * 10 + seed % 10;
                    seed /= 10;
                }
                ans
            })
            .collect()
    }
}
```
