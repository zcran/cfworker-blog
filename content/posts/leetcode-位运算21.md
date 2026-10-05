---
title: "leetcode-位运算21"
date: 2026-09-24T10:33:35+08:00
tags: ["leetcode", "位运算"]
draft: false
---


## N 天后的牢房

监狱中 8 间牢房排成一排，每间牢房可能被占用或空置。

每天，无论牢房是被占用或空置，都会根据以下规则进行变更：

如果一间牢房的两个相邻的房间都被占用或都是空的，那么该牢房就会被占用。
否则，它就会被空置。

注意：由于监狱中的牢房排成一行，所以行中的第一个和最后一个牢房不存在两个相邻的房间。

给你一个整数数组 cells ，用于表示牢房的初始状态：如果第 i 间牢房被占用，则 cell[i]==1，否则 cell[i]==0 。另给你一个整数 n 。

请你返回 n 天后监狱的状况（即，按上文描述进行 n 次变更）。


```
use std::collections::HashMap;

impl Solution {
    /// 模拟监狱牢房在 n 天后的状态。
    ///
    /// 使用 8 位整数表示牢房状态，低位对应第 0 间牢房。
    /// 由于状态空间最多只有 2^8 = 256 种，必然会出现循环，
    /// 因此用哈希表记录已出现状态的下标，发现循环后直接跳到对应天数的状态。
    pub fn prison_after_n_days(nums: Vec<i32>, n: i32) -> Vec<i32> {
        // 将初始状态压缩为 8 位整数
        let mut state = 0u8;
        for (i, &v) in nums.iter().enumerate() {
            if v == 1 {
                state |= 1 << i;
            }
        }

        // 记录状态出现顺序及其首次出现的位置
        let mut seen: HashMap<u8, i32> = HashMap::new();
        let mut history: Vec<u8> = Vec::with_capacity(256);

        seen.insert(state, 0);
        history.push(state);

        let mut day = 0;
        while day < n {
            // 计算下一天状态：仅第 1..=6 间牢房可能被占用
            let mut next = 0u8;
            for i in 1..7 {
                let left = (state >> (i - 1)) & 1;
                let right = (state >> (i + 1)) & 1;
                if left == right {
                    next |= 1 << i;
                }
            }

            day += 1;

            // 若出现循环，则直接定位到剩余天数对应的历史状态
            if let Some(&first_day) = seen.get(&next) {
                let cycle_len = day - first_day;
                let remaining = (n - first_day) % cycle_len;
                state = history[(first_day + remaining) as usize];
                break;
            }

            seen.insert(next, day);
            history.push(next);
            state = next;
        }

        // 将 8 位整数展开为结果数组
        (0..8).map(|i| ((state >> i) & 1) as i32).collect()
    }
}
```
