---
title: "leetcode-几何24"
date: 2026-05-11T20:20:55+08:00
tags: ["leetcode", "几何"]
draft: false
---


## 统计圆内格点数目

给你一个二维整数数组 circles ，其中 circles[i] = [xi, yi, ri] 表示网格上圆心为 (xi, yi) 且半径为 ri 的第 i 个圆，返回出现在 至少一个 圆内的 格点数目 。

注意：

格点 是指整数坐标对应的点。
圆周上的点 也被视为出现在圆内的点。

```
impl Solution {
    /// 计算网格上至少被一个圆覆盖的整点个数
    ///
    /// # 算法思路
    /// 1. 确定所有圆能覆盖的最大横纵坐标边界（圆心 + 半径）
    /// 2. 将所有圆按半径从大到小排序，这样在检查点是否被圆覆盖时可以尽早找到满足条件的圆
    /// 3. 遍历边界内所有整数点，判断是否存在一个圆包含该点，若存在则计数
    ///
    /// # 复杂度
    /// - 时间复杂度：O(max_x * max_y * n)，n 为圆的个数，在最坏情况下网格可能很大（需注意）
    /// - 空间复杂度：O(n)，用于存储圆的预处理数据
    ///
    /// # 参数
    /// - `circles`: 每个圆由 `[x, y, radius]` 表示，坐标和半径均为整数
    ///
    /// # 返回
    /// 至少被一个圆覆盖的整点数量
    pub fn count_lattice_points(circles: Vec<Vec<i32>>) -> i32 {
        // 计算边界：所有圆可能覆盖的最大 x 和 y 坐标
        let max_x = circles.iter().map(|c| c[0] + c[2]).max().unwrap_or(0);
        let max_y = circles.iter().map(|c| c[1] + c[2]).max().unwrap_or(0);

        // 预处理：将每个圆转换为 (x, y, r²) 并按半径平方降序排序（等价于半径降序）
        let mut circles_data: Vec<(i64, i64, i64)> = circles
            .into_iter()
            .map(|c| {
                let x = c[0] as i64;
                let y = c[1] as i64;
                let r = c[2] as i64;
                (x, y, r * r) // 存储半径平方，避免重复计算
            })
            .collect();
        // 按半径平方降序：大圆在前，能更快命中点
        circles_data.sort_by(|a, b| b.2.cmp(&a.2));

        // 生成所有整数点，判断是否至少被一个圆包含，计数
        (0..=max_x)
            .flat_map(|i| (0..=max_y).map(move |j| (i as i64, j as i64)))
            .filter(|&(i, j)| {
                // 短路检查：按半径从大到小依次判断，遇到第一个包含该点的圆就返回 true
                circles_data
                    .iter()
                    .any(|&(x, y, r_sq)| (x - i) * (x - i) + (y - j) * (y - j) <= r_sq)
            })
            .count() as i32
    }
}
```
