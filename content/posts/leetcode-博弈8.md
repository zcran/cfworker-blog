---
title: "leetcode-博弈8"
date: 2026-04-09T20:51:31+08:00
tags: ["leetcode", "博弈"]
draft: false
---


## 石子游戏 V

几块石子 排成一行 ，每块石子都有一个关联值，关联值为整数，由数组 stoneValue 给出。

游戏中的每一轮：Alice 会将这行石子分成两个 非空行（即，左侧行和右侧行）；Bob 负责计算每一行的值，即此行中所有石子的值的总和。Bob 会丢弃值最大的行，Alice 的得分为剩下那行的值（每轮累加）。如果两行的值相等，Bob 让 Alice 决定丢弃哪一行。下一轮从剩下的那一行开始。

只 剩下一块石子 时，游戏结束。Alice 的分数最初为 0 。

返回 Alice 能够获得的最大分数 。


```
use std::cmp::max;

impl Solution {
    pub fn stone_game_v(stone_value: Vec<i32>) -> i32 {
        let n = stone_value.len();
        // 前缀和：prefix_sums[i] = stone_value[0..=i] 的和
        let prefix_sums: Vec<i32> = stone_value
            .iter()
            .scan(0, |acc, &x| {
                *acc += x;
                Some(*acc)
            })
            .collect();

        // dp[l][r] 表示区间 [l, r] 的最大得分，-1 表示未计算
        let mut dp = vec![vec![-1; n]; n];

        // 区间求和辅助函数（纯函数，无捕获）
        fn range_sum(prefix_sums: &[i32], l: usize, r: usize) -> i32 {
            if l == 0 {
                prefix_sums[r]
            } else {
                prefix_sums[r] - prefix_sums[l - 1]
            }
        }

        // 递归求解，使用函数指针传递 prefix_sums，避免泛型膨胀
        fn solve(l: usize, r: usize, prefix_sums: &[i32], dp: &mut Vec<Vec<i32>>) -> i32 {
            if l >= r {
                return 0;
            }
            if dp[l][r] != -1 {
                return dp[l][r];
            }

            let mut best = 0;
            // 枚举分割点 k，将区间分为 [l, k] 和 [k+1, r]
            for k in l..r {
                let left_sum = range_sum(prefix_sums, l, k);
                let right_sum = range_sum(prefix_sums, k + 1, r);

                let score = match left_sum.cmp(&right_sum) {
                    std::cmp::Ordering::Greater => {
                        // 左侧更大，只能取右侧子数组的得分
                        right_sum + solve(k + 1, r, prefix_sums, dp)
                    }
                    std::cmp::Ordering::Less => {
                        // 右侧更大，只能取左侧子数组的得分
                        left_sum + solve(l, k, prefix_sums, dp)
                    }
                    std::cmp::Ordering::Equal => {
                        // 两侧相等，可以任选一边，取最大值
                        let take_right = right_sum + solve(k + 1, r, prefix_sums, dp);
                        let take_left = left_sum + solve(l, k, prefix_sums, dp);
                        max(take_right, take_left)
                    }
                };
                best = max(best, score);
            }
            dp[l][r] = best;
            best
        }

        solve(0, n - 1, &prefix_sums, &mut dp)
    }
}
```
