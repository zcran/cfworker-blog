---
title: "leetcode-前缀和143"
date: 2026-08-25T10:02:55+08:00
tags: ["leetcode", "前缀和"]
draft: false
---


## 击败所有怪物的最小初始强度

给你一个整数数组 monsters，其中 monsters[i] 表示第 i 个怪物的强度。

同时给你一个二维整数数组 boosts，其中 boosts[i] = [li, ri, vi] 表示与下标在 [li, ri] 范围内的任意怪物战斗时，你的 临时加成 会增加 vi。加成范围可能会重叠，所有适用的加成值将被相加。

你以一个 非负 初始强度开始，并从左到右依次与怪物战斗。

对于下标为 i 的每个怪物：

令 bonus 为适用于怪物 i 的所有加成值之 和。
只有你的当前强度加上 bonus 至少 为 monsters[i] 时，你才能击败该怪物。
击败怪物后，你的当前强度会减少 monsters[i]。如果强度变为 负数，则将其设置为 0。

返回击败所有怪物所需的 最小 初始强度。

注意：临时加成仅用于确定是否可以击败当前怪物。它不会以其他方式改变你的当前强度。


```
impl Solution {
    pub fn min_initial_strength(monsters: Vec<i32>, boosts: Vec<Vec<i32>>) -> i64 {
        let n = monsters.len();

        // ---------- 1. 差分数组计算每个位置的加成 ----------
        let mut bonus = vec![0i64; n + 1];
        for b in &boosts {
            let l = b[0] as usize;
            let r = b[1] as usize;
            let v = b[2] as i64;
            bonus[l] += v;
            bonus[r + 1] -= v;
        }
        // 前缀和还原真实加成
        for i in 1..n {
            bonus[i] += bonus[i - 1];
        }

        // ---------- 2. 二分搜索最小初始强度 ----------
        // 下界：0（初始强度非负）
        // 上界：所有怪物强度之和（足够击败所有怪物）
        let mut left = -1i64;
        let mut right = monsters.iter().map(|&x| x as i64).sum::<i64>();

        while left + 1 < right {
            let mid = left + (right - left) / 2;
            let mut strength = mid;
            let mut ok = true;

            for i in 0..n {
                // 当前强度加上加成必须足以击败怪物
                if strength + bonus[i] < monsters[i] as i64 {
                    ok = false;
                    break;
                }
                // 击败怪物后强度减少，但不低于 0
                strength = (strength - monsters[i] as i64).max(0);
            }

            if ok {
                right = mid; // 可以击败，尝试更小的初始强度
            } else {
                left = mid; // 不够，需要更大的初始强度
            }
        }

        right
    }
}
```
