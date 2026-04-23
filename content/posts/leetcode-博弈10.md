---
title: "leetcode-博弈10"
date: 2026-04-09T20:51:31+08:00
tags: ["leetcode", "博弈"]
draft: false
---


## 石子游戏 VII

石子游戏中，爱丽丝和鲍勃轮流进行自己的回合，爱丽丝先开始 。

有 n 块石子排成一排。每个玩家的回合中，可以从行中 移除 最左边的石头或最右边的石头，并获得与该行中剩余石头值之 和 相等的得分。当没有石头可移除时，得分较高者获胜。

鲍勃发现他总是输掉游戏（可怜的鲍勃，他总是输），所以他决定尽力 减小得分的差值 。爱丽丝的目标是最大限度地 扩大得分的差值 。

给你一个整数数组 stones ，其中 stones[i] 表示 从左边开始 的第 i 个石头的值，如果爱丽丝和鲍勃都 发挥出最佳水平 ，请返回他们 得分的差值 。


```
// Stone Game VII 的优化解法
// 算法：区间 DP，dp[i][j] 表示在子数组 stones[i..=j] 中，当前玩家能获得的最大分数差（相对于对手）
// 最终答案是 dp[0][n-1]（表示先手玩家相对于对手的净胜分）
impl Solution {
    pub fn stone_game_vii(stones: Vec<i32>) -> i32 {
        let n = stones.len();
        // 计算前缀和，用于快速求区间和
        // prefix[i] = stones[0] + ... + stones[i-1]（标准前缀和，prefix[0]=0）
        // 这样区间 [l, r] 的和 = prefix[r+1] - prefix[l]
        let prefix: Vec<i32> = std::iter::once(0)
            .chain(stones.iter().scan(0, |acc, &x| {
                *acc += x;
                Some(*acc)
            }))
            .collect();

        // dp[l][r] 表示在区间 [l, r]（包含两端）上的最大分数差
        let mut dp = vec![vec![0; n]; n];

        // 初始化长度为 2 的区间：只有两个石子时，当前玩家只能移除一个，并得到另一个石子的分数
        // 为了让对手得到 0，当前玩家会移除较小的石子，获得较大的石子分数，所以差值就是 max(stones[l], stones[r])
        for l in 0..n - 1 {
            let r = l + 1;
            dp[l][r] = stones[l].max(stones[r]);
        }

        // 按区间长度递增顺序计算（长度从 3 到 n）
        for len in 3..=n {
            for l in 0..=n - len {
                let r = l + len - 1;

                // 当前玩家可以选择移除最左边的石子（stones[l]）或最右边的石子（stones[r]）
                // 移除后，剩余区间的和就是本轮得到的分数，然后减去对手在剩余区间上的最优差值
                // 因为对手也会采取最优策略，所以当前玩家的净胜分 = 本轮得分 - dp[剩余区间]

                // 情况1：移除 stones[l]，本轮得分 = 区间 [l+1, r] 的和 = prefix[r+1] - prefix[l+1]
                let score_remove_left = (prefix[r + 1] - prefix[l + 1]) - dp[l + 1][r];
                // 情况2：移除 stones[r]，本轮得分 = 区间 [l, r-1] 的和 = prefix[r] - prefix[l]
                let score_remove_right = (prefix[r] - prefix[l]) - dp[l][r - 1];

                // 当前玩家会选择使自己净胜分更大的操作
                dp[l][r] = score_remove_left.max(score_remove_right);
            }
        }

        // 返回整个数组上的最优净胜分
        dp[0][n - 1]
    }
}
```
