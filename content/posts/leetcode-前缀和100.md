---
title: "leetcode-前缀和100"
date: 2026-08-25T10:02:52+08:00
tags: ["leetcode", "前缀和"]
draft: false
---


## 从魔法师身上吸取的最大能量

在神秘的地牢中，n 个魔法师站成一排。每个魔法师都拥有一个属性，这个属性可以给你提供能量。有些魔法师可能会给你负能量，即从你身上吸取能量。

你被施加了一种诅咒，当你从魔法师 i 处吸收能量后，你将被立即传送到魔法师 (i + k) 处。这一过程将重复进行，直到你到达一个不存在 (i + k) 的魔法师为止。

换句话说，你将选择一个起点，然后以 k 为间隔跳跃，直到到达魔法师序列的末端，在过程中吸收所有的能量。

给定一个数组 energy 和一个整数k，返回你能获得的 最大 能量。


```
impl Solution {
    pub fn maximum_energy(mut energy: Vec<i32>, k: i32) -> i32 {
        let k = k as usize;
        let n = energy.len();
        let mut ans = i32::MIN;

        // 从后往前累加，energy[i] 变为从 i 开始跳跃的所有能量和
        for i in (0..n).rev() {
            if i + k < n {
                energy[i] += energy[i + k];
            }
            ans = ans.max(energy[i]);
        }

        ans
    }
}
```
