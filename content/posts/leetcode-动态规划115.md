---
title: "leetcode-动态规划115"
date: 2026-10-05T20:40:01+08:00
tags: ["leetcode", "动态规划"]
draft: false
---


## 字符串连接删减字母

给你一个下标从 0 开始的数组 words ，它包含 n 个字符串。

定义 连接 操作 join(x, y) 表示将字符串 x 和 y 连在一起，得到 xy 。如果 x 的最后一个字符与 y 的第一个字符相等，连接后两个字符中的一个会被 删除 。

比方说 join("ab", "ba") = "aba" ， join("ab", "cde") = "abcde" 。

你需要执行 n - 1 次 连接 操作。令 strˇ0 = words[0] ，从 i = 1 直到 i = n - 1 ，对于第 i 个操作，你可以执行以下操作之一：

令 strˇi = join(strˇ(i - 1), words[i])
令 strˇi = join(words[i], strˇ(i - 1))

你的任务是使 strˇ(n - 1) 的长度 最小 。

请你返回一个整数，表示 strˇ(n - 1) 的最小长度。




```
use std::collections::HashMap;

impl Solution {
    pub fn minimize_concatenated_length(words: Vec<String>) -> i32 {
        // 总长度减去「因首尾相接而删除的字符数」即为最终最小长度
        let total: usize = words.iter().map(|w| w.len()).sum();

        // 对每个字符串，只关心其首字符和尾字符
        let info: Vec<(u8, u8)> = words
            .iter()
            .map(|w| {
                let b = w.as_bytes();
                (b[0], b[b.len() - 1])
            })
            .collect();

        // dp[(first, last)] = 当前拼接结果首字符为 first、尾字符为 last 时，
        // 已删除字符数的最大值
        let mut dp: HashMap<(u8, u8), usize> = HashMap::new();
        dp.insert(info[0], 0);

        for &(bf, bl) in &info[1..] {
            let mut ndp: HashMap<(u8, u8), usize> = HashMap::with_capacity(dp.len() * 2);
            for (&(af, al), &del) in &dp {
                // 方案一：把当前字符串接到右侧
                let add = if al == bf { 1 } else { 0 };
                let key = (af, bl);
                let v = del + add;
                ndp.entry(key).and_modify(|e| *e = (*e).max(v)).or_insert(v);

                // 方案二：把当前字符串接到左侧
                let add = if bl == af { 1 } else { 0 };
                let key = (bf, al);
                let v = del + add;
                ndp.entry(key).and_modify(|e| *e = (*e).max(v)).or_insert(v);
            }
            dp = ndp;
        }

        let max_deleted = dp.values().copied().max().unwrap_or(0);
        (total - max_deleted) as i32
    }
}
```
