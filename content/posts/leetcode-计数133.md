---
title: "leetcode-计数130"
date: 2026-08-01T10:40:58+08:00
tags: ["leetcode", "计数"]
draft: false
---


## 生存人数

给定 N 个人的出生年份和死亡年份，第 i 个人的出生年份为 birth[i]，死亡年份为 death[i]，实现一个方法以计算生存人数最多的年份。

你可以假设所有人都出生于 1900 年至 2000 年（含 1900 和 2000 ）之间。如果一个人在某一年的任意时期处于生存状态，那么他应该被纳入那一年的统计中。例如，生于 1908 年、死于 1909 年的人应当被列入 1908 年和 1909 年的计数。

如果有多个年份生存人数相同且均为最大值，输出其中最小的年份。


```
impl Solution {
    /// 计算生存人数最多的年份。
    ///
    /// 核心思路：差分数组（前缀和）。
    /// 对于生于 B、死于 D 的人，在 diff[B] 处 +1，在 diff[D+1] 处 -1。
    /// 对 diff 求前缀和即可得到每年的生存人数。
    pub fn max_alive_year(birth: Vec<i32>, death: Vec<i32>) -> i32 {
        const BASE: i32 = 1900;
        const YEARS: usize = 102; // 1900..=2000 共 101 年，再加 1 个用于 death+1

        let mut diff = [0i32; YEARS];

        // 构建差分数组
        for (b, d) in birth.into_iter().zip(death.into_iter()) {
            diff[(b - BASE) as usize] += 1;
            diff[(d - BASE + 1) as usize] -= 1;
        }

        // 前缀和扫描，取生存人数最多的最小年份
        let mut max_alive = 0;
        let mut result = BASE;
        let mut cur = 0;

        for i in 0..YEARS - 1 {
            // 只遍历到 2000 年（索引 100），索引 101 仅用于差分闭合
            cur += diff[i];
            if cur > max_alive {
                max_alive = cur;
                result = BASE + i as i32;
            }
        }

        result
    }
}
```
