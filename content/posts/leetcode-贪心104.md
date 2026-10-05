---
title: "leetcode-贪心104"
date: 2026-10-03T12:55:41+08:00
tags: ["leetcode", "贪心"]
draft: false
---


## 赢得比赛需要的最少训练时长

你正在参加一场比赛，给你两个 正 整数 initialEnergy 和 initialExperience 分别表示你的初始精力和初始经验。

另给你两个下标从 0 开始的整数数组 energy 和 experience，长度均为 n 。

你将会 依次 对上 n 个对手。第 i 个对手的精力和经验分别用 energy[i] 和 experience[i] 表示。当你对上对手时，需要在经验和精力上都 严格 超过对手才能击败他们，然后在可能的情况下继续对上下一个对手。

击败第 i 个对手会使你的经验 增加 experience[i]，但会将你的精力 减少  energy[i] 。

在开始比赛前，你可以训练几个小时。每训练一个小时，你可以选择将增加经验增加 1 或者 将精力增加 1 。

返回击败全部 n 个对手需要训练的 最少 小时数目。


```
impl Solution {
    pub fn min_number_of_hours(
        initial_energy: i32,
        initial_experience: i32,
        energy: Vec<i32>,
        experience: Vec<i32>,
    ) -> i32 {
        // 精力：需要始终严格大于所有对手精力之和，
        // 因为战斗过程中精力单调递减，只需保证初始精力足够撑完全程。
        let total_energy: i32 = energy.iter().sum();
        let mut hours = (total_energy - initial_energy + 1).max(0);

        // 经验：逐场模拟，经验只会增加。
        // 若当前经验不足以严格超过对手，则训练补足差值 + 1。
        let mut exp = initial_experience;
        for &e in &experience {
            if exp <= e {
                hours += e - exp + 1;
                exp = e + 1;
            }
            exp += e;
        }
        hours
    }
}
```
