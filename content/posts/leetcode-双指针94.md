---
title: "leetcode-双指针94"
date: 2026-09-05T09:53:06+08:00
tags: ["leetcode", "双指针"]
draft: false
---


## 划分技能点相等的团队

给你一个正整数数组 skill ，数组长度为 偶数 n ，其中 skill[i] 表示第 i 个玩家的技能点。将所有玩家分成 n / 2 个 2 人团队，使每一个团队的技能点之和 相等 。

团队的 化学反应 等于团队中玩家的技能点 乘积 。

返回所有团队的 化学反应 之和，如果无法使每个团队的技能点之和相等，则返回 -1 。


```
impl Solution {
    pub fn divide_players(mut skill: Vec<i32>) -> i64 {
        skill.sort_unstable();
        let n = skill.len();

        // 排序后，最小与最大配对，其和即为所有团队的目标和
        let target = skill[0] as i64 + skill[n - 1] as i64;
        let mut result = 0i64;

        for i in 0..n / 2 {
            let (a, b) = (skill[i] as i64, skill[n - 1 - i] as i64);
            // 校验每对的和是否都等于目标和
            if a + b != target {
                return -1;
            }
            result += a * b;
        }

        result
    }
}
```
