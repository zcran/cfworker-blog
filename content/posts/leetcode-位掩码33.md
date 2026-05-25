---
title: "leetcode-位掩码33"
date: 2026-05-18T10:27:59+08:00
tags: ["leetcode", "位掩码"]
draft: false
---


## 破解锁的最少时间 I


Bob 被困在了一个地窖里，他需要破解 n 个锁才能逃出地窖，每一个锁都需要一定的 能量 才能打开。每一个锁需要的能量存放在一个数组 strength 里，其中 strength[i] 表示打开第 i 个锁需要的能量。

Bob 有一把剑，它具备以下的特征：

· 一开始剑的能量为 0 。
· 剑的能量增加因子 X 一开始的值为 1 。
· 每分钟，剑的能量都会增加当前的 X 值。
· 打开第 i 把锁，剑的能量需要到达 至少 strength[i] 。
· 打开一把锁以后，剑的能量会变回 0 ，X 的值会增加一个给定的值 K 。

你的任务是打开所有 n 把锁并逃出地窖，请你求出需要的 最少 分钟数。

请你返回 Bob 打开所有 n 把锁需要的 最少 时间。


```
impl Solution {
    pub fn find_minimum_time(strength: Vec<i32>, k: i32) -> i32 {
        let n = strength.len();
        let mut ans = i32::MAX;
        let mut used = vec![false; n]; // 记录哪些锁已打开

        // 定义递归函数，使用闭包并通过参数传递自身以实现递归
        fn dfs(
            strength: &[i32],
            k: i32,
            idx: usize,      // 已经打开的锁数量（也是当前步数）
            time: i32,       // 已花费的时间
            used: &mut [bool],
            ans: &mut i32,
        ) {
            // 剪枝：当前时间已经不小于已知最优解，无需继续
            if time >= *ans {
                return;
            }
            // 所有锁都已打开，更新最优解
            if idx == strength.len() {
                *ans = time;
                return;
            }

            let x = 1 + k * (idx as i32); // 当前因子
            // 尝试打开每一个未使用的锁
            for j in 0..strength.len() {
                if !used[j] {
                    // 计算打开该锁所需时间：ceil(strength[j] / x)
                    let cost = (strength[j] + x - 1) / x;
                    used[j] = true;
                    dfs(strength, k, idx + 1, time + cost, used, ans);
                    used[j] = false; // 回溯
                }
            }
        }

        dfs(&strength, k, 0, 0, &mut used, &mut ans);
        ans
    }
}
```
