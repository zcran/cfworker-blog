---
title: "leetcode-树状树组26"
date: 2026-05-03T20:07:53+08:00
tags: ["leetcode", "树状树组"]
draft: false
---


## 用点构造面积最大的矩形 I


给你一个数组 points，其中 points[i] = [xi, yi] 表示无限平面上一点的坐标。

你的任务是找出满足以下条件的矩形可能的 最大 面积：

· 矩形的四个顶点必须是数组中的 四个 点。
· 矩形的内部或边界上 不能 包含任何其他点。
· 矩形的边与坐标轴 平行 。

返回可以获得的 最大面积 ，如果无法形成这样的矩形，则返回 -1。

```
use std::cmp::Ordering;

/// 树状数组（Fenwick Tree），支持单点增加和前缀和查询
struct Fenwick {
    tree: Vec<i32>,
}

impl Fenwick {
    /// 创建大小为 n 的树状数组（1-indexed）
    fn new(n: usize) -> Self {
        Self { tree: vec![0; n + 1] }
    }

    /// 在位置 i（1-indexed）增加 1
    fn add(&mut self, mut i: usize) {
        while i < self.tree.len() {
            self.tree[i] += 1;
            i += i & i.wrapping_neg(); // lowbit
        }
    }

    /// 前缀和 [1, i]
    fn prefix(&self, mut i: usize) -> i32 {
        let mut res = 0;
        while i > 0 {
            res += self.tree[i];
            i -= i & i.wrapping_neg();
        }
        res
    }

    /// 区间和 [l, r]（l, r 均为 1-indexed）
    fn range(&self, l: usize, r: usize) -> i32 {
        self.prefix(r) - self.prefix(l - 1)
    }
}

impl Solution {
    pub fn max_rectangle_area(points: Vec<Vec<i32>>) -> i64 {
        // 转换为元组，并按 x 升序，x 相同时按 y 升序（与 C++ 的 ranges::sort 行为一致）
        let mut pts: Vec<(i32, i32)> = points
            .into_iter()
            .map(|p| (p[0], p[1]))
            .collect();
        pts.sort_unstable_by(|a, b| a.0.cmp(&b.0).then(a.1.cmp(&b.1)));

        // 离散化 y 坐标
        let mut y_vals: Vec<i32> = pts.iter().map(|&(_, y)| y).collect();
        y_vals.sort_unstable();
        y_vals.dedup();
        let y_to_idx = |y: i32| -> usize { y_vals.binary_search(&y).unwrap() };

        let m = y_vals.len();
        let mut bit = Fenwick::new(m);
        let mut ans: i64 = -1;

        // 记录每个 y 值（离散化后）的最优左下角信息：(pre_x, pre_y, pre_points_count)
        let mut pre_info: Vec<(i32, i32, i32)> = vec![(-1, -1, -1); m];

        // 插入第一个点
        let first_y = pts[0].1;
        bit.add(y_to_idx(first_y) + 1);

        // 扫描排序后的点集
        for i in 1..pts.len() {
            let (x1, y1) = pts[i - 1];
            let (x2, y2) = pts[i];
            let y2_idx = y_to_idx(y2) + 1;
            bit.add(y2_idx);

            // 只有相邻两点 x 坐标相同，才能作为矩形的右边界（右下和右上）
            if x1 != x2 {
                continue;
            }

            let y1_idx = y_to_idx(y1) + 1;
            let cur = bit.range(y1_idx, y2_idx); // 区间 [y1, y2] 内的总点数
            let (pre_x, pre_y, pre_cnt) = pre_info[y2_idx - 1];

            // 构成矩形的条件：左下角的 y 等于当前右下角的 y，且区间内点数恰好比之前多了 2
            if pre_y == y1 && pre_cnt + 2 == cur {
                let area = (x2 - pre_x) as i64 * (y2 - y1) as i64;
                ans = ans.max(area);
            }

            // 更新当前 y2 对应的信息，供后续可能出现的矩形使用
            pre_info[y2_idx - 1] = (x1, y1, cur);
        }

        ans
    }
}
```
