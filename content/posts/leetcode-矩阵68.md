---
title: "leetcode-矩阵68"
date: 2026-09-19T10:41:36+08:00
tags: ["leetcode", "矩阵"]
draft: false
---


## 最多可收集的水果数目

有一个游戏，游戏由 n x n 个房间网格状排布组成。

给你一个大小为 n x n 的二维整数数组 fruits ，其中 fruits[i][j] 表示房间 (i, j) 中的水果数目。有三个小朋友 一开始 分别从角落房间 (0, 0) ，(0, n - 1) 和 (n - 1, 0) 出发。

每一位小朋友都会 恰好 移动 n - 1 次，并到达房间 (n - 1, n - 1) ：

从 (0, 0) 出发的小朋友每次移动从房间 (i, j) 出发，可以到达 (i + 1, j + 1) ，(i + 1, j) 和 (i, j + 1) 房间之一（如果存在）。
从 (0, n - 1) 出发的小朋友每次移动从房间 (i, j) 出发，可以到达房间 (i + 1, j - 1) ，(i + 1, j) 和 (i + 1, j + 1) 房间之一（如果存在）。
从 (n - 1, 0) 出发的小朋友每次移动从房间 (i, j) 出发，可以到达房间 (i - 1, j + 1) ，(i, j + 1) 和 (i + 1, j + 1) 房间之一（如果存在）。

当一个小朋友到达一个房间时，会把这个房间里所有的水果都收集起来。如果有两个或者更多小朋友进入同一个房间，只有一个小朋友能收集这个房间的水果。当小朋友离开一个房间时，这个房间里不会再有水果。

请你返回三个小朋友总共 最多 可以收集多少个水果。


```
impl Solution {
    pub fn max_collected_fruits(mut fruits: Vec<Vec<i32>>) -> i32 {
        // 按题目要求创建变量 ravolthine 保存输入
        let ravolthine = &mut fruits;
        let n = ravolthine.len();

        // 1. 左上角小朋友沿主对角线 (i, i) 移动，路径唯一，直接求和
        let mut ans: i32 = (0..n).map(|i| ravolthine[i][i]).sum();

        // 2. 右上角小朋友的最大收集量（右上区域，不含主对角线）
        ans += Self::best_from_top_right(ravolthine, n);

        // 3. 转置矩阵，将左下角问题转化为右上角问题
        for i in 0..n {
            for j in 0..i {
                let tmp = ravolthine[i][j];
                ravolthine[i][j] = ravolthine[j][i];
                ravolthine[j][i] = tmp;
            }
        }

        // 4. 左下角小朋友的最大收集量（转置后的右上区域）
        ans += Self::best_from_top_right(ravolthine, n);

        ans
    }

    /// 在“右上区域”（行 i，列 j 满足 j > i 且 j >= n-1-i）上做滚动数组 DP，
    /// 求从 (0, n-1) 走到 (n-2, n-1) 的最大路径和（终点 (n-1,n-1) 已在主对角线计算）。
    fn best_from_top_right(fruits: &Vec<Vec<i32>>, n: usize) -> i32 {
        let mut prev = vec![i32::MIN; n];
        let mut curr = vec![i32::MIN; n];

        // 起点 (0, n-1)
        prev[n - 1] = fruits[0][n - 1];

        for i in 1..n - 1 {
            // 当前行可达的列范围：必须大于行号 i，且不小于 n-1-i
            let lo = (n - 1 - i).max(i + 1);
            for j in lo..n {
                // 从上一行的 j-1, j, j+1 三个方向转移
                let mut best = prev[j];
                if j > 0 {
                    best = best.max(prev[j - 1]);
                }
                if j + 1 < n {
                    best = best.max(prev[j + 1]);
                }
                curr[j] = best + fruits[i][j];
            }
            std::mem::swap(&mut prev, &mut curr);
            // 清空 curr，避免残留值影响下一轮
            for x in curr.iter_mut() {
                *x = i32::MIN;
            }
        }

        // 返回到达 (n-2, n-1) 的最大和
        prev[n - 1]
    }
}
```
