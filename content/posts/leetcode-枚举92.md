---
title: "leetcode-枚举92"
date: 2026-07-09T10:05:03+08:00
tags: ["leetcode", "枚举"]
draft: false
---


## 使所有字符相等的最小删除代价

给你一个长度为 n 的字符串 s 和一个整数数组 cost，其中 cost[i] 表示 删除 字符串 s 中第 i 个字符的代价。

你可以从字符串 s 中删除任意数量的字符（也可以不删除），使得最终的字符串 非空 且由 相同 字符组成。

返回实现上述目标所需的 最小 总删除代价。


```
impl Solution {
    pub fn min_cost(s: String, cost: Vec<i32>) -> i64 {
        // 思路：保留某个字母的所有出现，删除其他所有字符
        // 对于每个字母，计算保留该字母时的代价 = 总代价 - 该字母的代价和
        // 答案 = 总代价 - 最大字母代价和

        let bytes = s.as_bytes();
        let mut total: i64 = 0;
        let mut sum = [0i64; 26];

        // 一次遍历累加总代价和各字母代价和
        for (&ch, &c) in bytes.iter().zip(cost.iter()) {
            let c64 = c as i64;
            total += c64;
            sum[(ch - b'a') as usize] += c64;
        }

        // 找到最大字母代价和
        let max_sum = sum.iter().max().unwrap();
        total - max_sum
    }
}
```
