---
title: "leetcode-动态规划62"
date: 2026-10-05T20:39:58+08:00
tags: ["leetcode", "动态规划"]
draft: false
---


## 多边形三角剖分的最低得分

你有一个凸的 n 边形，其每个顶点都有一个整数值。给定一个整数数组 values ，其中 values[i] 是按 顺时针顺序 第 i 个顶点的值。

假设将多边形 剖分 为 n - 2 个三角形。对于每个三角形，该三角形的值是顶点标记的乘积，三角剖分的分数是进行三角剖分后所有 n - 2 个三角形的值之和。

返回 多边形进行三角剖分后可以得到的最低分 。


```
impl Solution {
    /// 1039. 多边形三角剖分的最低得分
    ///
    /// 区间 DP：dp[i][j] 表示从顶点 i 到 j 组成的多边形剖分成三角形的最小得分。
    /// 枚举分割点 k（i < k < j），dp[i][j] = min(dp[i][k] + dp[k][j] + v[i]*v[j]*v[k])。
    /// 边界：j == i + 1 时只有一条边，得分 0。
    ///
    /// 按区间长度递增迭代，避免递归栈开销，也无需记忆化数组标记。
    pub fn min_score_triangulation(values: Vec<i32>) -> i32 {
        let n = values.len();
        // dp[i][j]：顶点 i..=j 构成的子多边形的最低得分
        let mut dp = vec![vec![0i32; n]; n];

        // 区间长度从 2 开始（至少 3 个顶点才有三角形：i, i+1, i+2）
        for len in 3..=n {
            for i in 0..=n - len {
                let j = i + len - 1;
                dp[i][j] = i32::MAX;
                let (vi, vj) = (values[i], values[j]);
                for k in i + 1..j {
                    let score = dp[i][k] + dp[k][j] + vi * vj * values[k];
                    if score < dp[i][j] {
                        dp[i][j] = score;
                    }
                }
            }
        }

        dp[0][n - 1]
    }
}
```
