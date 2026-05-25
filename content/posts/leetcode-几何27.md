---
title: "leetcode-几何27"
date: 2026-05-11T20:20:56+08:00
tags: ["leetcode", "几何"]
draft: false
---


## 求交集区域内的最大正方形面积

在二维平面上存在 n 个矩形。给你两个下标从 0 开始的二维整数数组 bottomLeft 和 topRight，两个数组的大小都是 n x 2 ，其中 bottomLeft[i] 和 topRight[i] 分别代表第 i 个矩形的 左下角 和 右上角 坐标。

我们定义 向右 的方向为 x 轴正半轴（x 坐标增加），向左 的方向为 x 轴负半轴（x 坐标减少）。同样地，定义 向上 的方向为 y 轴正半轴（y 坐标增加），向下 的方向为 y 轴负半轴（y 坐标减少）。

你可以选择一个区域，该区域由两个矩形的 交集 形成。你需要找出能够放入该区域 内 的 最大 正方形面积，并选择最优解。

返回能够放入交集区域的正方形的 最大 可能面积，如果矩形之间不存在任何交集区域，则返回 0。

```
impl Solution {
    /// 计算任意两个矩形重叠区域中能得到的最大正方形面积
    ///
    /// # 参数
    /// - `bottom_left`: 每个矩形的左下角坐标 `[x1, y1]`
    /// - `top_right`:   每个矩形的右上角坐标 `[x2, y2]`
    ///
    /// # 返回
    /// 最大正方形面积（64 位整数，避免溢出）
    ///
    /// # 示例
    /// ```
    /// let bl = vec![vec![0,0], vec![1,1]];
    /// let tr = vec![vec![2,2], vec![3,3]];
    /// assert_eq!(Solution::largest_square_area(bl, tr), 1);
    /// ```
    pub fn largest_square_area(bottom_left: Vec<Vec<i32>>, top_right: Vec<Vec<i32>>) -> i64 {
        let n = bottom_left.len();
        // 将所有矩形的边界提取为元组，便于后续直接访问
        let rects: Vec<(i32, i32, i32, i32)> = (0..n)
            .map(|i| {
                (
                    bottom_left[i][0], bottom_left[i][1],
                    top_right[i][0],   top_right[i][1],
                )
            })
            .collect();

        // 生成所有无序对 (i, j)，其中 i < j
        let max_side = (0..n)
            .flat_map(|i| (i + 1..n).map(move |j| (i, j)))
            .filter_map(|(i, j)| {
                let (ax1, ay1, ax2, ay2) = rects[i];
                let (bx1, by1, bx2, by2) = rects[j];
                // 计算重叠矩形的宽度和高度（若为负则无重叠）
                let w = (ax2.min(bx2) - ax1.max(bx1)).max(0);
                let h = (ay2.min(by2) - ay1.max(by1)).max(0);
                let side = w.min(h);
                if side > 0 { Some(side) } else { None }
            })
            .max()
            .unwrap_or(0);

        (max_side as i64) * (max_side as i64)
    }
}
```
