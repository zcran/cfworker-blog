---
title: "leetcode-博弈5"
date: 2026-04-09T20:51:30+08:00
tags: ["leetcode", "博弈"]
draft: false
---


## 石子游戏 II

Alice 和 Bob 继续他们的石子游戏。许多堆石子 排成一行，每堆都有正整数颗石子 piles[i]。游戏以谁手中的石子最多来决出胜负。

Alice 和 Bob 轮流进行，Alice 先开始。最初，M = 1。

在每个玩家的回合中，该玩家可以拿走剩下的 前 X 堆的所有石子，其中 1 <= X <= 2M。然后，令 M = max(M, X)。

游戏一直持续到所有石子都被拿走。

假设 Alice 和 Bob 都发挥出最佳水平，返回 Alice 可以得到的最大数量的石头。

```
impl Solution {
    pub fn stone_game_ii(piles: Vec<i32>) -> i32 {
        let n = piles.len();                     // 石子堆总数

        // 计算后缀和数组，长度为 n+1，suffix_sum[n] = 0 作为哨兵
        let mut suffix_sum = vec![0; n + 1];
        for i in (0..n).rev() {                 // 从 n-1 向下遍历到 0
            suffix_sum[i] = suffix_sum[i + 1] + piles[i];
        }

        // 记忆化表：memo[i][m] 存储 dp(i, m) 的结果
        // 第二维长度 n+1 覆盖 m 可能的最大值（初始 1，但 new_m 最大可到 n）
        let mut memo = vec![vec![None; n + 1]; n];

        // 定义递归函数 solve（实际是内部函数，借用外部变量）
        fn solve(
            i: usize,
            m: usize,
            n: usize,
            suffix_sum: &[i32],
            memo: &mut Vec<Vec<Option<i32>>>,
        ) -> i32 {
            // 情况1：没有石子堆可拿
            if i >= n {
                return 0;
            }
            // 情况2：可以一次性拿走所有剩余堆（因为最多取 2m 堆，而剩余堆数 ≤ 2m）
            if i + 2 * m >= n {
                return suffix_sum[i];
            }
            // 情况3：已经计算过，直接返回缓存值
            if let Some(cached) = memo[i][m] {
                return cached;
            }

            let mut best = 0;
            // 枚举本次取的石子堆数 x，范围 1..=2m
            for x in 1..=2 * m {
                let next_i = i + x;              // 对手的起始索引
                if next_i > n {                  // 防止越界（实际不会，因为上面已处理 i+2m>=n 情况）
                    break;
                }
                let new_m = m.max(x);             // 对手的新 M 值
                let opponent_best = solve(next_i, new_m, n, suffix_sum, memo);
                let current = suffix_sum[i] - opponent_best; // 当前玩家获得 = 剩余总和 - 对手最优
                best = best.max(current);
            }

            memo[i][m] = Some(best);              // 记忆化存储
            best
        }

        // 调用初始状态：从第 0 堆开始，M = 1，返回 Alice 能获得的最大石子数
        solve(0, 1, n, &suffix_sum, &mut memo)
    }
}


纯函数核心：solve 只依赖输入参数和 memo 缓存，无外部副作用。
不可变优先：suffix_sum 只读，best 通过 max 更新而非直接赋值（体现函数式累加思想）。
自然剪枝：if i + 2*m >= n 直接返回后缀和，清晰表达「能一次拿完」的边界条件。
变量命名自解释：next_i、new_m、opponent_best、current 一目了然。
使用 let 绑定中间结果：避免重复计算 suffix_sum[i] 等。
循环提前退出：if next_i > n { break; } 避免无效迭代。
