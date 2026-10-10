---
title: "leetcode-动态规划150"
date: 2026-10-05T20:40:04+08:00
tags: ["leetcode", "动态规划"]
draft: false
---


## 统计美丽整数的数目

给你两个正整数 l 和 r 。如果正整数每一位上的数字的乘积可以被这些数字之和整除，则认为该整数是一个 美丽整数 。

统计并返回 l 和 r 之间（包括 l 和 r ）的 美丽整数 的数目。


```
use std::collections::HashMap;

/// 数位 DP 的状态（缓存键）：
/// `i` 为当前处理的数位下标（从低位 0 开始），
/// `m` 为已选数字的乘积，`s` 为已选数字的和。
/// 由于只在不受限（`!is_limit`）且已开始选数字（`is_num`）时缓存，
/// `is_limit` 和 `is_num` 无需进入键。
#[derive(Hash, PartialEq, Eq)]
struct Key {
    i: usize,
    m: i64,
    s: i32,
}

struct Solver {
    digits: Vec<u8>,          // 反转后的数位（低位在前）
    memo: HashMap<Key, i32>,  // 记忆化缓存
}

impl Solver {
    fn new(r: i32) -> Self {
        let mut digits: Vec<u8> = r
            .to_string()
            .bytes()
            .rev()
            .map(|b| b - b'0')
            .collect();
        // 处理 r == 0 的边界（本题 l, r 为正整数，但保持健壮性）
        if digits.is_empty() {
            digits.push(0);
        }
        Self {
            digits,
            memo: HashMap::new(),
        }
    }

    /// 数位 DP：
    /// - `i`：当前数位下标（低位为 0）
    /// - `m`：已填数字的乘积（初始为 1）
    /// - `s`：已填数字的和（初始为 0）
    /// - `is_limit`：是否受到上界约束
    /// - `is_num`：是否已开始填数字（用于跳过前导零）
    fn dfs(&mut self, i: isize, m: i64, s: i32, is_limit: bool, is_num: bool) -> i32 {
        if i < 0 {
            // 终止条件：已填过数字，且和不为 0，且乘积能被和整除
            return (s != 0 && m % s as i64 == 0) as i32;
        }

        let idx = i as usize;
        if !is_limit && is_num {
            let key = Key { i: idx, m, s };
            if let Some(&v) = self.memo.get(&key) {
                return v;
            }
        }

        // 当前位可选的最大数字
        let hi = if is_limit { self.digits[idx] } else { 9 };

        // 情况一：继续不填数字（跳过前导零）
        let mut res = if is_num {
            0
        } else {
            self.dfs(i - 1, m, s, false, false)
        };

        // 情况二：枚举当前位填的数字
        // 若尚未开始填数字，则最低从 1 开始，避免前导零
        let lo = if is_num { 0 } else { 1 };
        for d in lo..=hi {
            let d_i32 = d as i32;
            res += self.dfs(
                i - 1,
                m * d as i64,
                s + d_i32,
                is_limit && d == hi,
                true,
            );
        }

        if !is_limit && is_num {
            self.memo.insert(Key { i: idx, m, s }, res);
        }
        res
    }
}

impl Solution {
    /// 计算 [1, r] 中美丽整数的个数
    fn calc(r: i32) -> i32 {
        if r <= 0 {
            return 0;
        }
        let mut solver = Solver::new(r);
        let n = solver.digits.len() as isize;
        solver.dfs(n - 1, 1, 0, true, false)
    }

    pub fn beautiful_numbers(l: i32, r: i32) -> i32 {
        Self::calc(r) - Self::calc(l - 1)
    }
}
```
