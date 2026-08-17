---
title: "leetcode-计数103"
date: 2026-08-01T10:40:56+08:00
tags: ["leetcode", "计数"]
draft: false
---


## 施咒的最大总伤害

一个魔法师有许多不同的咒语。

给你一个数组 power ，其中每个元素表示一个咒语的伤害值，可能会有多个咒语有相同的伤害值。

已知魔法师使用伤害值为 power[i] 的咒语时，他们就 不能 使用伤害为 power[i] - 2 ，power[i] - 1 ，power[i] + 1 或者 power[i] + 2 的咒语。

每个咒语最多只能被使用 一次 。

请你返回这个魔法师可以达到的伤害值之和的 最大值 。


```
impl Solution {
    pub fn maximum_total_damage(mut power: Vec<i32>) -> i64 {
        power.sort_unstable();

        // 去重并统计频率
        let mut spells = Vec::new();
        let mut i = 0;
        while i < power.len() {
            let mut j = i + 1;
            while j < power.len() && power[j] == power[i] {
                j += 1;
            }
            spells.push((power[i], (j - i) as i64));
            i = j;
        }

        let m = spells.len();
        // dp[i] = 选第 i 个咒语时的最大总伤害
        let mut dp = vec![0i64; m];
        let mut ans = 0i64;
        let mut best = 0i64;   // 不冲突前缀中的最大 dp 值
        let mut left = 0usize; // 双指针左边界

        for right in 0..m {
            let (dmg, cnt) = spells[right];
            // 扩展左边界，直到与当前冲突（差 <= 2）
            while left < right && spells[left].0 < dmg - 2 {
                best = best.max(dp[left]);
                left += 1;
            }
            dp[right] = best + dmg as i64 * cnt;
            ans = ans.max(dp[right]);
        }

        ans
    }
}
```
