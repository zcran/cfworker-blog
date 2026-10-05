---
title: "leetcode-二分查找69"
date: 2026-09-28T12:29:14+08:00
tags: ["leetcode", "二分查找"]
draft: false
---


## 最大合金数

假设你是一家合金制造公司的老板，你的公司使用多种金属来制造合金。现在共有 n 种不同类型的金属可以使用，并且你可以使用 k 台机器来制造合金。每台机器都需要特定数量的每种金属来创建合金。

对于第 i 台机器而言，创建合金需要 composition[i][j] 份 j 类型金属。最初，你拥有 stock[x] 份 x 类型金属，而每购入一份 x 类型金属需要花费 cost[x] 的金钱。

给你整数 n、k、budget，下标从 1 开始的二维数组 composition，两个下标从 1 开始的数组 stock 和 cost，请你在预算不超过 budget 金钱的前提下，最大化 公司制造合金的数量。

所有合金都需要由同一台机器制造。

返回公司可以制造的最大合金数。


```
impl Solution {
    pub fn max_number_of_alloys(
        n: i32,
        _k: i32,
        budget: i32,
        composition: Vec<Vec<i32>>,
        stock: Vec<i32>,
        cost: Vec<i32>,
    ) -> i32 {
        let budget = budget as i64;
        let stock: Vec<i64> = stock.into_iter().map(|x| x as i64).collect();
        let cost: Vec<i64> = cost.into_iter().map(|x| x as i64).collect();
        let n = n as usize;

        // 计算每台机器单独能制造的最大数量上界
        let mut upper = 0_i64;
        for comp in &composition {
            let mut max_for_machine = i64::MAX;
            for j in 0..n {
                let need = comp[j] as i64;
                if need > 0 {
                    // 现有库存 + 用预算能购买的数量
                    let can_buy = stock[j] + budget / cost[j];
                    max_for_machine = max_for_machine.min(can_buy / need);
                }
            }
            upper = upper.max(max_for_machine);
        }

        // 判断是否存在一台机器能制造至少 num 份合金
        let can_make = |num: i64| -> bool {
            composition.iter().any(|comp| {
                let mut money = 0_i64;
                for j in 0..n {
                    let need = comp[j] as i64 * num;
                    if need > stock[j] {
                        money += (need - stock[j]) * cost[j];
                        if money > budget {
                            return false;
                        }
                    }
                }
                true
            })
        };

        // 二分最大可制造数量
        let mut left = 0;
        let mut right = upper;
        while left < right {
            let mid = left + (right - left + 1) / 2;
            if can_make(mid) {
                left = mid;
            } else {
                right = mid - 1;
            }
        }
        left as i32
    }
}
```
