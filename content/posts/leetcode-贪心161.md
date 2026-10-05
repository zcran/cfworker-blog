---
title: "leetcode-贪心161"
date: 2026-10-03T12:55:45+08:00
tags: ["leetcode", "贪心"]
draft: false
---


## 数位平方和的最大值

给你两个 正 整数 num 和 sum。

如果一个正整数 n 满足以下两个条件，则称其为 好整数 ：

n 的位数 恰好 为 num。
n 的各位数字之和 恰好 为 sum。

一个 好整数 n 的 分数 定义为 n 的各位数字的平方和。

返回一个 字符串 ，表示能获得 最大分数 的 好整数 n。如果有多个可能的整数，返回 最大 的那个。如果不存在这样的整数，返回一个空字符串。




```
impl Solution {
    pub fn max_sum_of_squares(num: i32, sum: i32) -> String {
        // 位数不够容纳数位和时无解
        if num * 9 < sum {
            return String::new();
        }

        // 分数 = 各位数字平方和，要最大化分数就要让数字尽量大，
        // 同时又要整体字典序最大，故高位优先填 9。
        // 用尽可能多的 9 消耗 sum，余数放最后一位，其余补 0。
        let nines = sum / 9;
        let rem = sum % 9;

        let mut ans = "9".repeat(nines as usize);
        if rem > 0 {
            ans.push((b'0' + rem as u8) as char);
        }
        ans.push_str(&"0".repeat((num - ans.len() as i32) as usize));
        ans
    }
}
```
