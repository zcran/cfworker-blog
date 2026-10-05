---
title: "leetcode-贪心106"
date: 2026-10-03T12:55:41+08:00
tags: ["leetcode", "贪心"]
draft: false
---


## 完成所有交易的初始最少钱数

给你一个下标从 0 开始的二维整数数组 transactions，其中transactions[i] = [costi, cashbacki] 。

数组描述了若干笔交易。其中每笔交易必须以 某种顺序 恰好完成一次。在任意一个时刻，你有一定数目的钱 money ，为了完成交易 i ，money >= costi 这个条件必须为真。执行交易后，你的钱数 money 变成 money - costi + cashbacki 。

请你返回 任意一种 交易顺序下，你都能完成所有交易的最少钱数 money 是多少。


```
impl Solution {
    pub fn minimum_money(transactions: Vec<Vec<i32>>) -> i64 {
        // 结论：
        // 1. 无论何种顺序，净亏损（cost > cashback 的部分）都必须由初始资金承担，
        //    所以累加所有 max(cost - cashback, 0)。
        // 2. 对每笔交易，为了避免"先做某笔净赚交易时钱不够"的极端顺序，
        //    还需额外覆盖所有交易中 min(cost, cashback) 的最大值。
        let mut base = 0i64;   // 所有交易的净亏损之和
        let mut extra = 0i32;  // 需要额外覆盖的最大 min(cost, cashback)

        for t in &transactions {
            let cost = t[0];
            let cashback = t[1];
            base += (cost - cashback).max(0) as i64;
            extra = extra.max(cost.min(cashback));
        }
        base + extra as i64
    }
}
```
