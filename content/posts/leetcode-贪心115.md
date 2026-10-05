---
title: "leetcode-贪心115"
date: 2026-10-03T12:55:41+08:00
tags: ["leetcode", "贪心"]
draft: false
---


## 将钱分给最多的儿童

给你一个整数 money ，表示你总共有的钱数（单位为美元）和另一个整数 children ，表示你要将钱分配给多少个儿童。

你需要按照如下规则分配：

所有的钱都必须被分配。
每个儿童至少获得 1 美元。
没有人获得 4 美元。

请你按照上述规则分配金钱，并返回 最多 有多少个儿童获得 恰好 8 美元。如果没有任何分配方案，返回 -1 。


```
impl Solution {
    pub fn dist_money(money: i32, children: i32) -> i32 {
        // 每个孩子至少 1 美元，钱不够则无解
        if money < children {
            return -1;
        }
        // 钱太多，无法让所有孩子都恰好 8 美元，最多 children - 1 个
        if money > 8 * children {
            return children - 1;
        }
        // 特殊情况：总钱数比所有孩子都拿 8 美元少 4 美元。
        // 若让一个孩子拿 4 美元，其余拿 8 美元，违反规则，必须再牺牲一个名额
        if money == 8 * children - 4 {
            return children - 2;
        }
        // 一般情况：先给每个孩子 1 美元，剩余钱每 7 美元可让一个孩子达到 8 美元
        (money - children) / 7
    }
}
```
