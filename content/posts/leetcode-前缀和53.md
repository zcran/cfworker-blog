---
title: "leetcode-前缀和53"
date: 2026-08-25T10:02:49+08:00
tags: ["leetcode", "前缀和"]
draft: false
---


## 蜡烛之间的盘子

给你一个长桌子，桌子上盘子和蜡烛排成一列。给你一个下标从 0 开始的字符串 s ，它只包含字符 '\*' 和 '|' ，其中 '\*' 表示一个 盘子 ，'|' 表示一支 蜡烛 。

同时给你一个下标从 0 开始的二维整数数组 queries ，其中 queries[i] = [lefti, righti] 表示 子字符串 s[lefti...righti] （包含左右端点的字符）。对于每个查询，你需要找到 子字符串中 在 两支蜡烛之间 的盘子的 数目 。如果一个盘子在 子字符串中 左边和右边 都 至少有一支蜡烛，那么这个盘子满足在 两支蜡烛之间 。

比方说，s = "||\**||\**|\*" ，查询 [3, 8] ，表示的是子字符串 "\*|**|" 。子字符串中在两支蜡烛之间的盘子数目为 2 ，子字符串中右边两个盘子在它们左边和右边 都 至少有一支蜡烛。

请你返回一个整数数组 answer ，其中 answer[i] 是第 i 个查询的答案。


```
impl Solution {
    pub fn plates_between_candles(s: String, queries: Vec<Vec<i32>>) -> Vec<i32> {
        let bytes = s.as_bytes();
        let n = s.len();

        // prev_candle[i] = 在位置 i 左侧（包含 i）最近的蜡烛位置
        let mut prev_candle = vec![-1; n];
        // next_candle[i] = 在位置 i 右侧（包含 i）最近的蜡烛位置
        let mut next_candle = vec![-1; n];
        // candle_prefix[i] = 前 i 个字符中蜡烛的数量
        let mut candle_prefix = vec![0; n + 1];

        // 从左到右预处理：最近蜡烛和蜡烛前缀和
        for i in 0..n {
            if bytes[i] == b'|' {
                prev_candle[i] = i as i32;
                candle_prefix[i + 1] = candle_prefix[i] + 1;
            } else {
                prev_candle[i] = if i > 0 { prev_candle[i - 1] } else { -1 };
                candle_prefix[i + 1] = candle_prefix[i];
            }
        }

        // 从右到左预处理：右侧最近蜡烛
        for i in (0..n).rev() {
            if bytes[i] == b'|' {
                next_candle[i] = i as i32;
            } else {
                next_candle[i] = if i + 1 < n { next_candle[i + 1] } else { -1 };
            }
        }

        // 处理每个查询
        let mut ans = Vec::with_capacity(queries.len());
        for query in queries {
            let left = query[0] as usize;
            let right = query[1] as usize;

            // 找到区间内最左边和最右边的蜡烛
            let left_candle = next_candle[left];
            let right_candle = prev_candle[right];

            if left_candle == -1 || right_candle == -1 || left_candle >= right_candle {
                ans.push(0);
            } else {
                // 盘子数 = (右蜡烛索引 - 左蜡烛索引 + 1) - 蜡烛数
                let candles = candle_prefix[right_candle as usize + 1] - candle_prefix[left_candle as usize];
                let total = right_candle - left_candle + 1;
                ans.push(total - candles);
            }
        }

        ans
    }
}
```
