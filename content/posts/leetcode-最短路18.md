---
title: "leetcode-最短路18"
date: 2026-04-22T20:45:20+08:00
tags: ["leetcode", "最短路"]
draft: false
---


## 转换字符串的最小成本 I

给你两个下标从 0 开始的字符串 source 和 target ，它们的长度均为 n 并且由 小写 英文字母组成。

另给你两个下标从 0 开始的字符数组 original 和 changed ，以及一个整数数组 cost ，其中 cost[i] 代表将字符 original[i] 更改为字符 changed[i] 的成本。

你从字符串 source 开始。在一次操作中，如果 存在 任意 下标 j 满足 cost[j] == z  、original[j] == x 以及 changed[j] == y 。你就可以选择字符串中的一个字符 x 并以 z 的成本将其更改为字符 y 。

返回将字符串 source 转换为字符串 target 所需的 最小 成本。如果不可能完成转换，则返回 -1 。

注意，可能存在下标 i 、j 使得 original[j] == original[i] 且 changed[j] == changed[i] 。


```
impl Solution {
    /// 计算将字符串 source 转换为 target 的最小总代价。
    /// 每次操作可以将一个字符转换为另一个字符，代价由 original/changed/cost 给出（允许间接转换）。
    /// 如果无法转换（某位置不可达），返回 -1。
    pub fn minimum_cost(
        source: String,
        target: String,
        original: Vec<char>,
        changed: Vec<char>,
        cost: Vec<i32>,
    ) -> i64 {
        const INF: i32 = 0x3f3f3f3f;   // 表示无穷大（不可达）
        const ALPHABET: usize = 26;

        // 初始化距离矩阵：自己到自己的距离为 0，其余为 INF
        let mut dist = [[INF; ALPHABET]; ALPHABET];
        for i in 0..ALPHABET {
            dist[i][i] = 0;
        }

        // 根据提供的直接转换边更新距离矩阵（取最小代价）
        for (&from, &to, &c) in original.iter().zip(changed.iter()).zip(cost.iter()).map(|((a, b), c)| (a, b, c)) {
            let u = (from as u8 - b'a') as usize;
            let v = (to as u8 - b'a') as usize;
            if c < dist[u][v] {
                dist[u][v] = c;
            }
        }

        // Floyd-Warshall 求所有字符对的最短转换代价
        for k in 0..ALPHABET {
            for i in 0..ALPHABET {
                if dist[i][k] == INF {
                    continue; // 无法通过 k 中转，跳过
                }
                // 内层循环可并行，这里保持函数式风格但使用 for 循环保证性能
                for j in 0..ALPHABET {
                    let via = dist[i][k] + dist[k][j];
                    if via < dist[i][j] {
                        dist[i][j] = via;
                    }
                }
            }
        }

        // 计算结果：遍历 source 和 target 的每个字符，累加转换代价
        source
            .chars()
            .zip(target.chars())
            .try_fold(0i64, |acc, (sc, tc)| {
                let u = (sc as u8 - b'a') as usize;
                let v = (tc as u8 - b'a') as usize;
                if dist[u][v] == INF {
                    None // 存在不可达字符，转换失败
                } else {
                    Some(acc + dist[u][v] as i64)
                }
            })
            .unwrap_or(-1) // 如果失败则返回 -1
    }
}
```
