---
title: "leetcode-贪心143"
date: 2026-10-03T12:55:43+08:00
tags: ["leetcode", "贪心"]
draft: false
---


## 与敌人战斗后的最大分数

给你一个下标从 0 开始的整数数组 enemyEnergies ，它表示一个下标从 0 开始的敌人能量数组。

同时给你一个整数 currentEnergy ，它表示你一开始拥有的能量值总量。

你一开始的分数为 0 ，且一开始所有的敌人都未标记。

你可以通过以下操作 之一 任意次（也可以 0 次）来得分：

· 选择一个 未标记 且满足 currentEnergy >= enemyEnergies[i] 的敌人 i 。在这个操作中：
    · 你会获得 1 分。
    · 你的能量值减少 enemyEnergies[i] ，也就是说 currentEnergy = currentEnergy - enemyEnergies[i] 。
· 如果你目前 至少 有 1 分，你可以选择一个 未标记 的敌人 i 。在这个操作中：
    · 你的能量值增加 enemyEnergies[i] ，也就是说 currentEnergy = currentEnergy + enemyEnergies[i] 。
    · 敌人 i 被标记 。

请你返回通过以上操作，最多 可以获得多少分。


```
impl Solution {
    pub fn maximum_points(enemy_energies: Vec<i32>, current_energy: i32) -> i64 {
        // 贪心思路：
        // 1. 最小能量的敌人最划算，用它反复刷分。
        // 2. 但必须先能击败它一次（currentEnergy >= min），否则得 0 分。
        // 3. 击败最小敌人得分后，用「标记敌人换能量」的操作，
        //    把其余敌人（包括最初的最小敌人）能量全部吸收，
        //    总能量 = currentEnergy + 所有敌人能量之和 - 最小敌人能量（第一次击败已消耗）。
        // 4. 之后每得 1 分需消耗 min 能量，故总分为 (总能量) / min。
        let min = *enemy_energies.iter().min().unwrap();
        if current_energy < min {
            return 0;
        }

        let total: i64 = enemy_energies.iter().map(|&x| x as i64).sum();
        (current_energy as i64 + total - min as i64) / min as i64
    }
}
```
