---
title: "leetcode-贪心146"
date: 2026-10-03T12:55:43+08:00
tags: ["leetcode", "贪心"]
draft: false
---


## 对 Bob 造成的最少伤害

给你一个整数 power 和两个整数数组 damage 和 health ，两个数组的长度都为 n 。

Bob 有 n 个敌人，如果第 i 个敌人还活着（也就是健康值 health[i] > 0 的时候），每秒钟会对 Bob 造成 damage[i] 点 伤害。

每一秒中，在敌人对 Bob 造成伤害 之后 ，Bob 会选择 一个 还活着的敌人进行攻击，该敌人的健康值减少 power 。

请你返回 Bob 将 所有 n 个敌人都消灭之前，最少 会受到多少伤害。


```
impl Solution {
    pub fn min_damage(power: i32, damage: Vec<i32>, health: Vec<i32>) -> i64 {
        // 每个敌人需要的攻击次数 k = ceil(health / power)。
        // 敌人每秒持续造成 damage，因此应优先击杀「单位攻击次数伤害高」的敌人，
        // 即按 k_i * d_j < k_j * d_i 排序（避免浮点，交叉相乘比较）。
        let mut enemies: Vec<(i64, i64)> = health
            .iter()
            .zip(damage.iter())
            .map(|(&h, &d)| {
                let k = ((h - 1) / power + 1) as i64; // 击杀所需攻击次数
                (k, d as i64)
            })
            .collect();

        enemies.sort_unstable_by(|a, b| (a.0 * b.1).cmp(&(b.0 * a.1)));

        let mut total_time = 0i64; // 累计攻击次数（即总耗时）
        let mut ans = 0i64;
        for (k, d) in enemies {
            total_time += k;
            ans += total_time * d; // 该敌人在被消灭前持续造成的伤害
        }
        ans
    }
}
```
