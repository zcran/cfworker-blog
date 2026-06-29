---
title: "leetcode-数论46"
date: 2026-06-20T11:09:56+08:00
tags: ["leetcode", "数论"]
draft: false
---


## 统计不是特殊数字的数字数量

给你两个 正整数 l 和 r。对于任何数字 x，x 的所有正因数（除了 x 本身）被称为 x 的 真因数。

如果一个数字恰好仅有两个 真因数，则称该数字为 特殊数字。例如：

数字 4 是 特殊数字，因为它的真因数为 1 和 2。

数字 6 不是 特殊数字，因为它的真因数为 1、2 和 3。

返回区间 [l, r] 内 不是 特殊数字 的数字数量。


```
impl Solution {
    pub fn non_special_count(l: i32, r: i32) -> i32 {
        // 特殊数字是质数的平方（因为质数p只有真因数1和p）
        // 所以问题转化为：统计区间内非质数平方的个数

        let limit = (r as f64).sqrt() as usize; // 只需检查到 sqrt(r)
        let mut is_composite = vec![false; limit + 1]; // 标记合数（非质数）
        let mut special_count = 0; // 区间内特殊数字的个数

        // 埃氏筛找出所有质数
        for i in 2..=limit {
            if !is_composite[i] {
                // i 是质数，计算其平方
                let square = (i * i) as i32;
                if square >= l && square <= r {
                    special_count += 1; // 质数的平方是特殊数字
                }

                // 标记 i 的所有倍数为合数（从 i*i 开始，避免重复标记）
                for multiple in (i * i..=limit).step_by(i) {
                    is_composite[multiple] = true;
                }
            }
        }

        // 区间总数减去特殊数字个数
        (r - l + 1) - special_count
    }
}
```
