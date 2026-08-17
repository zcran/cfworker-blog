---
title: "leetcode-模拟44"
date: 2026-08-08T11:31:11+08:00
tags: ["leetcode", "模拟"]
draft: false
---


## 积压订单中的订单总数

给你一个二维整数数组 orders ，其中每个 orders[i] = [pricei, amounti, orderTypei] 表示有 amounti 笔类型为 orderTypei 、价格为 pricei 的订单。

订单类型 orderTypei 可以分为两种：

· 0 表示这是一批采购订单 buy
· 1 表示这是一批销售订单 sell

注意，orders[i] 表示一批共计 amounti 笔的独立订单，这些订单的价格和类型相同。对于所有有效的 i ，由 orders[i] 表示的所有订单提交时间均早于 orders[i+1] 表示的所有订单。

存在由未执行订单组成的 积压订单 。积压订单最初是空的。提交订单时，会发生以下情况：

· 如果该订单是一笔采购订单 buy ，则可以查看积压订单中价格 最低 的销售订单 sell 。如果该销售订单 sell 的价格 低于或等于 当前采购订单 buy 的价格，则匹配并执行这两笔订单，并将销售订单 sell 从积压订单中删除。否则，采购订单 buy 将会添加到积压订单中。

· 反之亦然，如果该订单是一笔销售订单 sell ，则可以查看积压订单中价格 最高 的采购订单 buy 。如果该采购订单 buy 的价格 高于或等于 当前销售订单 sell 的价格，则匹配并执行这两笔订单，并将采购订单 buy 从积压订单中删除。否则，销售订单 sell 将会添加到积压订单中。

输入所有订单后，返回积压订单中的 订单总数 。由于数字可能很大，所以需要返回对 10^9 + 7 取余的结果。


```
impl Solution {
    pub fn get_number_of_backlog_orders(orders: Vec<Vec<i32>>) -> i32 {
        use std::collections::BinaryHeap;
        use std::cmp::Reverse;

        const MOD: i64 = 1_000_000_007;
        let mut buy: BinaryHeap<(i32, i64)> = BinaryHeap::new();
        let mut sell: BinaryHeap<Reverse<(i32, i64)>> = BinaryHeap::new();

        for order in orders {
            let (price, mut amount, order_type) = (order[0], order[1] as i64, order[2]);

            if order_type == 0 {
                // 采购：匹配价格最低的销售订单
                while amount > 0 {
                    match sell.peek() {
                        Some(&Reverse((sp, sa))) if sp <= price => {
                            let min = amount.min(sa);
                            amount -= min;
                            if sa == min { sell.pop(); }
                            else { sell.peek_mut().unwrap().0.1 -= min; }
                        }
                        _ => break,
                    }
                }
                if amount > 0 { buy.push((price, amount)); }
            } else {
                // 销售：匹配价格最高的采购订单
                while amount > 0 {
                    match buy.peek() {
                        Some(&(bp, ba)) if bp >= price => {
                            let min = amount.min(ba);
                            amount -= min;
                            if ba == min { buy.pop(); }
                            else { buy.peek_mut().unwrap().1 -= min; }
                        }
                        _ => break,
                    }
                }
                if amount > 0 { sell.push(Reverse((price, amount))); }
            }
        }

        let total = buy.iter().map(|(_, a)| a).sum::<i64>()
                  + sell.iter().map(|r| r.0.1).sum::<i64>();
        (total % MOD) as i32
    }
}
```
