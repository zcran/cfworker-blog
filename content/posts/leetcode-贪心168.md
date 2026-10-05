---
title: "leetcode-贪心168"
date: 2026-10-03T12:55:45+08:00
tags: ["leetcode", "贪心"]
draft: false
---


## 使二进制字符串相等的最小成本

给你两个长度为 n 的二进制字符串 s 和 t，以及三个 正整数 flipCost、swapCost 和 crossCost。

你可以对字符串 s 和 t 应用以下操作任意次（顺序不限）：

· 选择任意下标 i，翻转 s[i] 或 t[i]（将 '0' 变为 '1' 或将 '1' 变为 '0'）。此操作的成本为 flipCost。
· 选择两个 不同 下标 i 和 j，交换 s[i] 和 s[j] 或 t[i] 和 t[j]。此操作的成本为 swapCost。
· 选择一个下标 i，交换 s[i] 和 t[i]。此操作的成本为 crossCost。

返回使字符串 s 和 t 相等所需的 最小总成本。




```
impl Solution {
    pub fn minimum_cost(s: String, t: String, flip_cost: i32, swap_cost: i32, cross_cost: i32) -> i64 {
        // 统计 s 与 t 在每个位置上不同的情况：
        //   cnt[0][1] = s 为 0、t 为 1 的位置数
        //   cnt[1][0] = s 为 1、t 为 0 的位置数
        // 这两种"错配"需要修正。
        let (mut a, mut b) = (0i64, 0i64); // a 为较少的一类错配数
        for (x, y) in s.bytes().zip(t.bytes()) {
            if x != y {
                if x == b'0' {
                    a += 1; // s=0, t=1
                } else {
                    b += 1; // s=1, t=0
                }
            }
        }
        if a > b {
            std::mem::swap(&mut a, &mut b);
        }

        let (fc, sc, cc) = (flip_cost as i64, swap_cost as i64, cross_cost as i64);

        // 策略 1：全部用翻转修正
        let plan1 = (a + b) * fc;

        // 策略 2：用交换处理配对的错配（a 对），剩余全部翻转
        let plan2 = a * sc + (b - a) * fc;

        // 策略 3：用交叉交换 + 内部交换处理，剩余奇偶性用翻转补齐
        let avg = (a + b) / 2;
        let plan3 = (avg - a) * cc + avg * sc + ((a + b) % 2) * fc;

        plan1.min(plan2).min(plan3)
    }
}

```
