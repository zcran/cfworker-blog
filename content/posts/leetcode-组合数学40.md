---
title: "leetcode-组合数学40"
date: 2026-05-24T09:54:14+08:00
tags: ["leetcode", "组合数学"]
draft: false
---


## 判断操作后字符串中的数字是否相等 II


给你一个由数字组成的字符串 s 。重复执行以下操作，直到字符串恰好包含 两个 数字：

从第一个数字开始，对于 s 中的每一对连续数字，计算这两个数字的和 模 10。
用计算得到的新数字依次替换 s 的每一个字符，并保持原本的顺序。

如果 s 最后剩下的两个数字相同，则返回 true 。否则，返回 false。

```
// 预计算组合数 C(n,k) mod 5 静态表
lazy_static! {
    static ref COMB_MOD5: [[u8; 6]; 6] = {
        let mut tbl = [[0u8; 6]; 6];
        tbl[0][0] = 1;
        for i in 1..=5 {
            tbl[i][0] = 1;
            for j in 1..=i {
                tbl[i][j] = (tbl[i-1][j-1] + tbl[i-1][j]) % 5;
            }
        }
        tbl
    };
}

impl Solution {
    pub fn has_same_digits(s: String) -> bool {
        // 转数字数组，非数字直接返回 false
        let digits: Vec<u8> = s.chars()
            .map(|c| c.to_digit(10).map(|d| d as u8))
            .collect::<Option<_>>()
            .unwrap_or_default();

        if digits.len() != s.len() {
            return false;
        }

        let n = digits.len();
        // 边界条件
        if n == 1 {
            return true;
        }
        if n == 2 {
            return digits[0] == digits[1];
        }

        let m = n - 2;
        let mut comb = vec![0u8; m + 1];

        // 计算 C(m, i) mod 10（卢卡斯定理 + 中国剩余定理）
        for i in 0..=m {
            // 模 2 结果
            let c2 = if (i & m) == i { 1 } else { 0 };
            // 模 5 结果
            let mut c5 = 1u8;
            let (mut x, mut y) = (m, i);
            while x > 0 || y > 0 {
                c5 = (c5 * COMB_MOD5[(x % 5) as usize][(y % 5) as usize]) % 5;
                x /= 5;
                y /= 5;
            }
            // 合并模 2 和模 5 结果得到模 10
            comb[i] = ((c2 * 5 + c5 as u32 * 6) % 10) as u8;
        }

        // 计算最终两个数字
        let mut left = 0u8;
        let mut right = 0u8;
        for i in 0..=m {
            left = (left + digits[i] * comb[i]) % 10;
            right = (right + digits[i + 1] * comb[i]) % 10;
        }

        left == right
    }
}
```
