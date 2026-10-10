---
title: "leetcode-数学52"
date: 2026-10-07T20:39:31+08:00
tags: ["leetcode", "数学"]
draft: false
---


## 判断一个数是否迷人

给你一个三位数整数 n 。

如果经过以下修改得到的数字 恰好 包含数字 1 到 9 各一次且不包含任何 0 ，那么我们称数字 n 是 迷人的 ：

将 n 与数字 2 * n 和 3 * n 连接 。

如果 n 是迷人的，返回 true，否则返回 false 。

连接 两个数字表示把它们首尾相接连在一起。比方说 121 和 371 连接得到 121371 。


```
impl Solution {
    pub fn is_fascinating(n: i32) -> bool {
        // 将 n、2n、3n 的每一位数字计数，
        // 要求 1..9 各出现恰好一次，且 0 不出现。
        let mut count = [0u8; 10];

        for mut x in [n, 2 * n, 3 * n] {
            while x > 0 {
                count[(x % 10) as usize] += 1;
                x /= 10;
            }
        }

        // count[0] 必须为 0，count[1..=9] 必须全为 1
        count[0] == 0 && count[1..].iter().all(|&c| c == 1)
    }
}
```
