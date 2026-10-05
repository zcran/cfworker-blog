---
title: "leetcode-双指针86"
date: 2026-09-05T09:53:06+08:00
tags: ["leetcode", "双指针"]
draft: false
---


## 咒语和药水的成功对数

给你两个正整数数组 spells 和 potions ，长度分别为 n 和 m ，其中 spells[i] 表示第 i 个咒语的能量强度，potions[j] 表示第 j 瓶药水的能量强度。

同时给你一个整数 success 。一个咒语和药水的能量强度 相乘 如果 大于等于 success ，那么它们视为一对 成功 的组合。

请你返回一个长度为 n 的整数数组 pairs，其中 pairs[i] 是能跟第 i 个咒语成功组合的 药水 数目。


```
impl Solution {
    pub fn successful_pairs(mut spells: Vec<i32>, mut potions: Vec<i32>, success: i64) -> Vec<i32> {
        potions.sort_unstable();

        // 对每个咒语，二分找到第一个满足 spell * potion >= success 的药水位置
        spells.iter_mut().for_each(|spell| {
            let target = (success + *spell as i64 - 1) / *spell as i64; // 向上取整，避免浮点误差
            let idx = potions.partition_point(|&potion| (potion as i64) < target);
            *spell = (potions.len() - idx) as i32;
        });

        spells
    }
}
```
