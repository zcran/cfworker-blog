---
title: "leetcode-组合数学6"
date: 2026-05-24T09:54:12+08:00
tags: ["leetcode", "组合数学"]
draft: false
---


## 两个盒子中球的颜色数相同的概率


桌面上有 2n 个颜色不完全相同的球，球的颜色共有 k 种。给你一个大小为 k 的整数数组 balls ，其中 balls[i] 是颜色为 i 的球的数量。

所有的球都已经 随机打乱顺序 ，前 n 个球放入第一个盒子，后 n 个球放入另一个盒子（请认真阅读示例 2 的解释部分）。

注意：这两个盒子是不同的。例如，两个球颜色分别为 a 和 b，盒子分别为 [] 和 ()，那么 [a] (b) 和 [b] (a) 这两种分配方式是不同的（请认真阅读示例的解释部分）。

请返回「两个盒子中球的颜色数相同」的情况的概率。答案与真实值误差在 10-5 以内，则被视为正确答案


```
impl Solution {
    pub fn get_probability(balls: Vec<i32>) -> f64 {
        let n = balls.len();
        let total_balls: i32 = balls.iter().sum();
        let half = total_balls / 2;                      // 每个盒子最终应拥有的球数
        let max_ball = *balls.iter().max().unwrap() as usize;

        // 阶乘表，用于组合数计算
        let mut fact = vec![1i64; max_ball + 1];
        for i in 1..=max_ball {
            fact[i] = fact[i - 1] * i as i64;
        }

        // 2的幂表，用于计算每种颜色分配的概率
        let mut pow2 = vec![1.0f64; max_ball + 1];
        for i in 1..=max_ball {
            pow2[i] = pow2[i - 1] * 2.0;
        }

        // 后缀和：left[i] = 颜色 i..n-1 的球总数，用于剪枝
        let mut left = vec![0i32; n];
        left[n - 1] = balls[n - 1];
        for i in (0..n - 1).rev() {
            left[i] = left[i + 1] + balls[i];
        }

        // 分母：所有合法分配（左右球数相等）的概率
        let mut denom = 1.0;
        for i in 1..=half {
            denom *= (i as f64 + half as f64) / (i as f64) / 4.0;
        }

        // 深度优先搜索，返回满足条件的概率
        fn dfs(
            balls: &[i32],
            left: &[i32],
            fact: &[i64],
            pow2: &[f64],
            idx: usize,
            sum_diff: i32,
            color_diff: i32,
        ) -> f64 {
            if idx == balls.len() {
                // 所有颜色分配完毕，要求左右球数相等且颜色种数相等
                return if sum_diff == 0 && color_diff == 0 { 1.0 } else { 0.0 };
            }

            // 剪枝：剩余球数不足以平衡当前差值
            if sum_diff.abs() > left[idx] {
                return 0.0;
            }

            let cnt = balls[idx] as usize; // 当前颜色的总球数
            let mut res = 0.0;

            // 枚举左边分配 i 个球
            for i in 0..=cnt {
                let left_cnt = i as i32;
                let right_cnt = cnt as i32 - left_cnt;
                let new_sum_diff = sum_diff + left_cnt - right_cnt;

                // 颜色种数变化：全在右 → -1，全在左 → +1，两边都有 → 0
                let delta_color = if i == 0 {
                    -1
                } else if i == cnt {
                    1
                } else {
                    0
                };

                // 组合数 C(cnt, i) * (1/2^cnt)  = 该颜色分配方式的概率
                let comb = fact[cnt] / (fact[i] * fact[cnt - i]);
                let prob_color = comb as f64 / pow2[cnt];

                res += prob_color
                    * dfs(
                        balls,
                        left,
                        fact,
                        pow2,
                        idx + 1,
                        new_sum_diff,
                        color_diff + delta_color,
                    );
            }
            res
        }

        let prob = dfs(&balls, &left, &fact, &pow2, 0, 0, 0);
        prob / denom
    }
}
```
