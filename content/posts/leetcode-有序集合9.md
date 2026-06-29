---
title: "leetcode-有序集合9"
date: 2026-06-14T10:47:41+08:00
tags: ["leetcode", "有序集合"]
draft: false
---


## 矩形面积 II

给你一个轴对齐的二维数组 rectangles 。 对于 rectangle[i] = [x1, y1, x2, y2]，其中 (xi1, yi1) 是该矩形 左下角 的坐标， (xi2, yi2) 是该矩形 右上角 的坐标。

计算平面中所有 rectangles 所覆盖的 总面积 。任何被两个或多个矩形覆盖的区域应只计算 一次 。

返回 总面积 。因为答案可能太大，返回 10^9 + 7 的 模 。


```
use std::collections::{BTreeMap, BTreeSet};

impl Solution {
    pub fn rectangle_area(rectangles: Vec<Vec<i32>>) -> i32 {
        const MOD: i64 = 1_000_000_007;

        // 1. 收集所有 y 坐标用于离散化
        let mut y_coords: Vec<i32> = rectangles
            .iter()
            .flat_map(|r| vec![r[1], r[3]])
            .collect();
        y_coords.sort_unstable();
        y_coords.dedup();

        // 2. 构建 y 坐标到索引的映射
        let y_to_idx: BTreeMap<i32, usize> = y_coords
            .iter()
            .enumerate()
            .map(|(i, &y)| (y, i))
            .collect();

        // 3. 构建扫描线事件
        let mut events = Vec::with_capacity(rectangles.len() * 2);
        for rect in rectangles {
            let x1 = rect[0];
            let y1 = rect[1];
            let x2 = rect[2];
            let y2 = rect[3];

            events.push((x1, y1, y2, 1));   // 左边界
            events.push((x2, y1, y2, -1));  // 右边界
        }

        events.sort_unstable_by_key(|e| e.0);

        // 4. 线段树：y_coords 有 n 个点，形成 n-1 个区间
        let n = y_coords.len();
        let mut seg_tree = SegmentTree::new(&y_coords);

        let mut total_area: i64 = 0;
        let mut prev_x = events[0].0;

        for i in 0..events.len() {
            let (x, y1, y2, delta) = events[i];

            // 计算面积
            let width = (x - prev_x) as i64;
            if width > 0 {
                let covered_height = seg_tree.query() as i64;
                total_area = (total_area + covered_height * width) % MOD;
            }

            // 更新覆盖：注意线段树叶子节点表示区间 [y_coords[i], y_coords[i+1])
            let left = y_to_idx[&y1];
            let right = y_to_idx[&y2] - 1;  // 因为线段树的叶子节点索引从0到n-2

            // 只有当 left <= right 时才更新
            if left <= right {
                seg_tree.update(left, right, delta);
            }

            prev_x = x;
        }

        total_area as i32
    }
}

/// 线段树 - 维护区间覆盖长度
struct SegmentTree {
    cover: Vec<i32>,   // 覆盖次数
    length: Vec<i32>,  // 当前覆盖的总长度
    max_len: Vec<i32>, // 区间完整长度
    n: usize,          // 叶子节点数量
}

impl SegmentTree {
    fn new(y_coords: &[i32]) -> Self {
        let n = y_coords.len() - 1;  // 叶子节点数 = 点数 - 1
        let size = n * 4 + 1;

        let mut tree = Self {
            cover: vec![0; size],
            length: vec![0; size],
            max_len: vec![0; size],
            n,
        };

        tree.init(1, 0, n - 1, y_coords);
        tree
    }

    /// 递归初始化线段树
    fn init(&mut self, node: usize, l: usize, r: usize, y_coords: &[i32]) {
        if l == r {
            // 叶子节点：表示区间 [y_coords[l], y_coords[l+1])
            self.max_len[node] = y_coords[l + 1] - y_coords[l];
            return;
        }

        let mid = (l + r) / 2;
        self.init(node * 2, l, mid, y_coords);
        self.init(node * 2 + 1, mid + 1, r, y_coords);
        self.max_len[node] = self.max_len[node * 2] + self.max_len[node * 2 + 1];
    }

    /// 更新区间 [ql, qr] 的覆盖次数
    fn update(&mut self, ql: usize, qr: usize, delta: i32) {
        if ql > qr || self.n == 0 {
            return;
        }
        self.update_rec(1, 0, self.n - 1, ql, qr, delta);
    }

    fn update_rec(
        &mut self,
        node: usize,
        l: usize,
        r: usize,
        ql: usize,
        qr: usize,
        delta: i32,
    ) {
        if ql > r || qr < l {
            return;
        }

        if ql <= l && r <= qr {
            // 完全覆盖
            self.cover[node] += delta;
            self.push_up(node, l, r);
            return;
        }

        let mid = (l + r) / 2;
        if ql <= mid {
            self.update_rec(node * 2, l, mid, ql, qr, delta);
        }
        if qr > mid {
            self.update_rec(node * 2 + 1, mid + 1, r, ql, qr, delta);
        }
        self.push_up(node, l, r);
    }

    /// 更新当前节点的覆盖长度
    fn push_up(&mut self, node: usize, l: usize, r: usize) {
        if self.cover[node] > 0 {
            // 当前节点被完全覆盖
            self.length[node] = self.max_len[node];
        } else if l == r {
            // 叶子节点且未被覆盖
            self.length[node] = 0;
        } else {
            // 非叶子节点：长度 = 子节点长度之和
            self.length[node] = self.length[node * 2] + self.length[node * 2 + 1];
        }
    }

    /// 查询当前被覆盖的总长度
    fn query(&self) -> i32 {
        if self.n == 0 {
            0
        } else {
            self.length[1]
        }
    }
}
```
