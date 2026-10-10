---
title: "leetcode-动态规划137"
date: 2026-10-05T20:40:03+08:00
tags: ["leetcode", "动态规划"]
draft: false
---


## 超级饮料的最大强化能量

来自未来的体育科学家给你两个整数数组 energyDrinkA 和 energyDrinkB，数组长度都等于 n。这两个数组分别代表 A、B 两种不同能量饮料每小时所能提供的强化能量。

你需要每小时饮用一种能量饮料来 最大化 你的总强化能量。然而，如果从一种能量饮料切换到另一种，你需要等待一小时来梳理身体的能量体系（在那个小时里你将不会获得任何强化能量）。

返回在接下来的 n 小时内你能获得的 最大 总强化能量。

注意 你可以选择从饮用任意一种能量饮料开始。


```
impl Solution {
    pub fn max_energy_boost(energy_drink_a: Vec<i32>, energy_drink_b: Vec<i32>) -> i64 {
        let n = energy_drink_a.len();

        // dp[0] / dp[1]：当前小时喝 A / B 时的最大总能量
        // 转移：要么延续同种饮料（上一小时状态），要么从另一种切换（需跳过一小时，即用上上小时状态）
        let mut dp_a = energy_drink_a[0] as i64;
        let mut dp_b = energy_drink_b[0] as i64;

        // 记录上上小时的状态，用于「切换需跳过一小时」的转移
        let mut prev_a = 0i64;
        let mut prev_b = 0i64;

        for i in 1..n {
            let a = energy_drink_a[i] as i64;
            let b = energy_drink_b[i] as i64;

            let new_a = dp_a.max(prev_b) + a;
            let new_b = dp_b.max(prev_a) + b;

            prev_a = dp_a;
            prev_b = dp_b;
            dp_a = new_a;
            dp_b = new_b;
        }

        dp_a.max(dp_b)
    }
}
```
