---
title: "leetcode-几何26"
date: 2026-05-11T20:20:55+08:00
tags: ["leetcode", "几何"]
draft: false
---


## 人员站位的方案数 I

给你一个  n x 2 的二维数组 points ，它表示二维平面上的一些点坐标，其中 points[i] = [xi, yi] 。



计算点对 (A, B) 的数量，其中

A 在 B 的左上角，并且
它们形成的长方形中（或直线上）没有其它点（包括边界），除了点 A 和点 B。
返回数量。

```
impl Solution {
    /// 统计满足条件的点对数量
    ///
    /// 条件：
    /// 1. 点 A 在点 B 的左上方（A.x <= B.x 且 A.y >= B.y）
    /// 2. 以 A 为左下角、B 为右上角构成的轴对齐矩形内（含边界）没有其他点
    ///
    /// # 参数
    /// - `points`：每个点由 `[x, y]` 表示，坐标均为整数
    ///
    /// # 返回
    /// 符合要求的点对 (A, B) 的数量，其中 A 和 B 是不同的点
    ///
    /// # 复杂度
    /// - 时间：O(n³)，其中 n 为点的个数（原算法复杂度）
    /// - 空间：O(n)，用于存储坐标元组
    pub fn number_of_pairs(points: Vec<Vec<i32>>) -> i32 {
        // 将点转换为元组 (x, y)，便于访问和避免多次索引
        let pts: Vec<(i32, i32)> = points
            .iter()
            .map(|p| (p[0], p[1]))
            .collect();
        let n = pts.len();

        // 生成所有有序对 (i, j)，i 和 j 不同，且 i 在 j 的左上方
        (0..n)
            .flat_map(|i| (0..n).map(move |j| (i, j)))
            .filter(|&(i, j)| {
                i != j && pts[i].0 <= pts[j].0 && pts[i].1 >= pts[j].1
            })
            .filter(|&(i, j)| {
                let (ax, ay) = pts[i];
                let (bx, by) = pts[j];
                // 检查是否存在另一个点 k 落在矩形内部（含边界）
                !(0..n).any(|k| {
                    k != i && k != j
                        && pts[k].0 >= ax && pts[k].0 <= bx
                        && pts[k].1 <= ay && pts[k].1 >= by
                })
            })
            .count() as i32
    }
}
```
