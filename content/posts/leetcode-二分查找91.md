---
title: "leetcode-二分查找91"
date: 2026-09-28T12:29:16+08:00
tags: ["leetcode", "二分查找"]
draft: false
---


## 使每个元素变为回文数的最少操作次数

给你一个整数数组 nums。

一次 操作 中，你可以选择一个下标 i，并将 nums[i] 增加 2 或减少 2。

返回将 nums 中的每个元素都变为 正回文整数 所需的 最少 操作次数。不同元素可以变成不同的回文整数。

如果一个整数正着读和反着读都相同，则称其为 回文整数 。例如，121 是回文整数，而 123 不是。


```
use std::sync::OnceLock;

const MX: i32 = 2_000_000_002;

// 预处理 [1, MX] 中的所有回文数，按奇偶性分成两组
fn palindromes() -> &'static [Vec<i32>; 2] {
    static PAL: OnceLock<[Vec<i32>; 2]> = OnceLock::new();
    PAL.get_or_init(|| {
        let mut pal: [Vec<i32>; 2] = [vec![0], vec![0]]; // 哨兵
        let mut base = 1_i64;
        loop {
            // 生成奇数长度回文数
            for i in base..base * 10 {
                let mut x = i;
                let mut t = i / 10;
                while t > 0 {
                    x = x * 10 + t % 10;
                    t /= 10;
                }
                if x > MX as i64 {
                    return pal;
                }
                pal[(x % 2) as usize].push(x as i32);
            }
            // 生成偶数长度回文数
            for i in base..base * 10 {
                let mut x = i;
                let mut t = i;
                while t > 0 {
                    x = x * 10 + t % 10;
                    t /= 10;
                }
                if x > MX as i64 {
                    return pal;
                }
                pal[(x % 2) as usize].push(x as i32);
            }
            base *= 10;
        }
    })
}

impl Solution {
    pub fn min_operations(nums: Vec<i32>) -> i64 {
        let pal = palindromes();
        let mut ans = 0_i64;

        for &x in &nums {
            // 操作步长为 2，所以只需考虑与 x 同奇偶的回文数
            let list = &pal[(x % 2) as usize];
            let i = list.partition_point(|&v| v < x);
            // 找左右最近的两个同奇偶回文数，取较小的距离
            let diff = (list[i] - x).min(x - list[i - 1]);
            ans += diff as i64;
        }

        ans / 2
    }
}
```
