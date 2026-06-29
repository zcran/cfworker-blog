---
title: "leetcode-单调栈24"
date: 2026-06-07T10:14:04+08:00
tags: ["leetcode", "单调栈"]
draft: false
---


## 统计全 1 子矩形

给你一个 m x n 的二进制矩阵 mat ，请你返回有多少个 子矩形 的元素全部都是 1 。

```
impl Solution {
    /// 统计全为 1 的矩形子矩阵数量
    ///
    /// 最可靠的单调栈方法：对每一行，统计以该行为底边的所有矩形
    /// 每个矩形由其底边和高度唯一确定
    pub fn num_submat(mat: Vec<Vec<i32>>) -> i32 {
        let m = mat.len();
        let n = mat[0].len();
        let mut res = 0;
        let mut heights = vec![0; n];

        for i in 0..m {
            // 更新柱状图高度
            for j in 0..n {
                heights[j] = if mat[i][j] == 1 { heights[j] + 1 } else { 0 };
            }

            // 对每一行使用单调栈统计矩形
            let mut stack: Vec<usize> = Vec::new();
            // dp[j] 表示以 j 为右边界的矩形数量
            let mut dp = vec![0; n];

            for j in 0..n {
                // 维护单调递增栈（存储索引）
                while !stack.is_empty() && heights[*stack.last().unwrap()] >= heights[j] {
                    stack.pop();
                }

                if !stack.is_empty() {
                    let prev = *stack.last().unwrap();
                    // 以 j 为右边界的矩形数 =
                    // 以 prev 为右边界的矩形数 + heights[j] * (j - prev)
                    dp[j] = dp[prev] + heights[j] * (j - prev) as i32;
                } else {
                    // 左边没有更小的，从 0 开始
                    dp[j] = heights[j] * (j + 1) as i32;
                }

                stack.push(j);
                res += dp[j];
            }
        }
        res
    }
}
```
