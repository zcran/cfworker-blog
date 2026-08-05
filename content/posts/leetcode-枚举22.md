---
title: "leetcode-枚举22"
date: 2026-07-09T10:05:00+08:00
tags: ["leetcode", "枚举"]
draft: false
---


## 设置时间的最少代价

常见的微波炉可以设置加热时间，且加热时间满足以下条件：

· 至少为 1 秒钟。
· 至多为 99 分 99 秒。

你可以 最多 输入 4 个数字 来设置加热时间。如果你输入的位数不足 4 位，微波炉会自动加 前缀 0 来补足 4 位。微波炉会将设置好的四位数中，前 两位当作分钟数，后 两位当作秒数。它们所表示的总时间就是加热时间。比方说：

· 你输入 9 5 4 （三个数字），被自动补足为 0954 ，并表示 9 分 54 秒。
· 你输入 0 0 0 8 （四个数字），表示 0 分 8 秒。
· 你输入 8 0 9 0 ，表示 80 分 90 秒。
· 你输入 8 1 3 0 ，表示 81 分 30 秒。

给你整数 startAt ，moveCost ，pushCost 和 targetSeconds 。一开始，你的手指在数字 startAt 处。将手指移到 任何其他数字 ，需要花费 moveCost 的单位代价。每 输入你手指所在位置的数字一次，需要花费 pushCost 的单位代价。

要设置 targetSeconds 秒的加热时间，可能会有多种设置方法。你想要知道这些方法中，总代价最小为多少。

请你能返回设置 targetSeconds 秒钟加热时间需要花费的最少代价。

请记住，虽然微波炉的秒数最多可以设置到 99 秒，但一分钟等于 60 秒。


```
impl Solution {
    /// 计算设置 targetSeconds 秒加热时间的最小代价。
    /// 最多输入 4 位数字，自动补前缀 0，前两位为分钟，后两位为秒。
    pub fn min_cost_set_time(start_at: i32, move_cost: i32, push_cost: i32, target_seconds: i32) -> i32 {
        let mut min_cost = i32::MAX;

        // 枚举秒数部分 (0..=99)
        for sec in 0..=99 {
            // 检查是否能由分钟和秒组合成 target_seconds
            if target_seconds < sec {
                continue;
            }
            let remaining = target_seconds - sec;
            if remaining % 60 != 0 {
                continue;
            }
            let min = remaining / 60;
            if min > 99 {
                continue;
            }

            // 构造输入字符串（去掉前导零，但保证秒数至少两位）
            let min_str = if min == 0 {
                String::new() // 分钟为 0 时不显示
            } else {
                min.to_string()
            };

            let sec_str = if sec < 10 && !min_str.is_empty() {
                format!("0{}", sec) // 分钟不为 0 时，秒数补前导零
            } else {
                sec.to_string()
            };

            let input = format!("{}{}", min_str, sec_str);

            // 计算输入该字符串的代价
            let cost = Self::calculate_cost(&input, start_at, move_cost, push_cost);
            min_cost = min_cost.min(cost);
        }

        min_cost
    }

    /// 计算输入指定字符串的代价
    #[inline]
    fn calculate_cost(input: &str, start_at: i32, move_cost: i32, push_cost: i32) -> i32 {
        let mut total_cost = 0;
        let mut current = start_at as u8;

        for ch in input.bytes() {
            let digit = ch - b'0'; // 转为数字 0..=9

            if digit == current {
                total_cost += push_cost;
            } else {
                total_cost += move_cost + push_cost;
                current = digit;
            }
        }

        total_cost
    }
}
```
