---
title: "leetcode-几何23"
date: 2026-05-11T20:20:55+08:00
tags: ["leetcode", "几何"]
draft: false
---


## 引爆最多的炸弹


给你一个炸弹列表。一个炸弹的 爆炸范围 定义为以炸弹为圆心的一个圆。

炸弹用一个下标从 0 开始的二维整数数组 bombs 表示，其中 bombs[i] = [xi, yi, ri] 。xi 和 yi 表示第 i 个炸弹的 X 和 Y 坐标，ri 表示爆炸范围的 半径 。

你需要选择引爆 一个 炸弹。当这个炸弹被引爆时，所有 在它爆炸范围内的炸弹都会被引爆，这些炸弹会进一步将它们爆炸范围内的其他炸弹引爆。

给你数组 bombs ，请你返回在引爆 一个 炸弹的前提下，最多 能引爆的炸弹数目。

```
use std::collections::VecDeque;

impl Solution {
    /// 计算最多可以引爆的炸弹数量
    ///
    /// 给定炸弹列表 `bombs`，每个炸弹由 `[x, y, radius]` 表示。
    /// 若炸弹 A 的爆炸范围（半径）能够覆盖炸弹 B 的中心，则 A 可以引爆 B（有向关系）。
    /// 引爆具有传递性：如果 A 引爆 B，B 引爆 C，那么 A 也能间接引爆 C。
    /// 返回从任意一个炸弹开始引爆，最多能引爆的炸弹总数（包括自身）。
    ///
    /// # 参数
    /// - `bombs`: 二维向量，每个元素为 `[x, y, radius]`，均为整数（i32）
    ///
    /// # 返回
    /// 最多能引爆的炸弹数量（i32）
    ///
    /// # 示例
    /// ```
    /// let bombs = vec![vec![2,1,3], vec![6,1,4]];
    /// assert_eq!(Solution::maximum_detonation(bombs), 2);
    /// ```
    pub fn maximum_detonation(bombs: Vec<Vec<i32>>) -> i32 {
        let n = bombs.len();
        // 预计算每个炸弹半径的平方（使用 i64 避免平方时溢出）
        let radii_sq: Vec<i64> = bombs
            .iter()
            .map(|b| b[2] as i64 * b[2] as i64)
            .collect();

        // 构建邻接表：edges[i] 存储炸弹 i 能直接引爆的所有炸弹索引
        let mut edges = vec![vec![]; n];
        for i in 0..n {
            let xi = bombs[i][0] as i64;
            let yi = bombs[i][1] as i64;
            let ri_sq = radii_sq[i];
            // 使用迭代器生成所有能被 i 引爆的 j，避免手动循环中的 if 分支
            edges[i] = (0..n)
                .filter(|&j| i != j) // 不能自己引爆自己
                .filter(|&j| {
                    let dx = xi - bombs[j][0] as i64;
                    let dy = yi - bombs[j][1] as i64;
                    dx * dx + dy * dy <= ri_sq
                })
                .collect();
        }

        // 对每个炸弹执行 BFS，统计可达的炸弹数量，并记录最大值
        (0..n)
            .map(|start| {
                let mut visited = vec![false; n];
                let mut queue = VecDeque::new();
                visited[start] = true;
                queue.push_back(start);
                let mut count = 1; // 自身也算引爆

                while let Some(cur) = queue.pop_front() {
                    for &nxt in &edges[cur] {
                        if !visited[nxt] {
                            visited[nxt] = true;
                            count += 1;
                            queue.push_back(nxt);
                        }
                    }
                }
                count
            })
            .max()
            .unwrap_or(0) as i32
    }
}
```
