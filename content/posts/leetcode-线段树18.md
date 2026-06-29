---
title: "leetcode-线段树18"
date: 2026-06-19T09:37:25+08:00
tags: ["leetcode", "线段树"]
draft: false
---


## 分割正方形 II

给你一个二维整数数组 squares ，其中 squares[i] = [xi, yi, li] 表示一个与 x 轴平行的正方形的左下角坐标和正方形的边长。

找到一个最小的 y 坐标，它对应一条水平线，该线需要满足它以上正方形的总面积 等于 该线以下正方形的总面积。

答案如果与实际答案的误差在 10^-5 以内，将视为正确答案。

注意：正方形 可能会 重叠。重叠区域只 统计一次 。


```
struct SegmentTree {
    count: Vec<i32>,
    covered: Vec<i32>,
    xs: Vec<i32>,
    n: usize,
}

impl SegmentTree {
    fn new(xs: Vec<i32>) -> Self {
        let n = xs.len() - 1;
        SegmentTree {
            count: vec![0; 4 * n],
            covered: vec![0; 4 * n],
            xs,
            n,
        }
    }

    fn modify(&mut self, qleft: i32, qright: i32, qval: i32,
              left: usize, right: usize, pos: usize) {
        if self.xs[right + 1] <= qleft || self.xs[left] >= qright {
            return;
        }
        if qleft <= self.xs[left] && self.xs[right + 1] <= qright {
            self.count[pos] += qval;
        } else {
            let mid = (left + right) / 2;
            self.modify(qleft, qright, qval, left, mid, pos * 2 + 1);
            self.modify(qleft, qright, qval, mid + 1, right, pos * 2 + 2);
        }

        if self.count[pos] > 0 {
            self.covered[pos] = self.xs[right + 1] - self.xs[left];
        } else {
            if left == right {
                self.covered[pos] = 0;
            } else {
                self.covered[pos] = self.covered[pos * 2 + 1] + self.covered[pos * 2 + 2];
            }
        }
    }

    fn update(&mut self, qleft: i32, qright: i32, qval: i32) {
        self.modify(qleft, qright, qval, 0, self.n - 1, 0);
    }

    fn query(&self) -> i32 {
        self.covered[0]
    }
}

impl Solution {
    pub fn separate_squares(squares: Vec<Vec<i32>>) -> f64 {
        // 存储事件: (y坐标, 类型, 左边界, 右边界)
        let mut events: Vec<(i32, i32, i32, i32)> = Vec::new();
        let mut xs_set = std::collections::BTreeSet::new();

        for sq in squares {
            let (x, y, l) = (sq[0], sq[1], sq[2]);
            let xr = x + l;
            events.push((y, 1, x, xr));
            events.push((y + l, -1, x, xr));
            xs_set.insert(x);
            xs_set.insert(xr);
        }

        // 按y坐标排序事件
        events.sort_by_key(|&(y, _, _, _)| y);
        // 离散化坐标
        let xs: Vec<i32> = xs_set.into_iter().collect();
        // 初始化线段树
        let mut seg_tree = SegmentTree::new(xs);

        let mut psum: Vec<i64> = Vec::new();
        let mut widths: Vec<i32> = Vec::new();
        let mut total_area = 0;
        let mut prev = events[0].0;

        // 扫描：计算总面积和记录中间状态
        for &(y, delta, xl, xr) in &events {
            let length = seg_tree.query();
            total_area += length as i64 * (y - prev) as i64;
            seg_tree.update(xl, xr, delta);
            // 记录前缀和和宽度
            psum.push(total_area);
            widths.push(seg_tree.query());
            prev = y;
        }

        // 计算目标面积（向上取整的一半）
        let target = ((total_area as f64 + 1.0) / 2.0).floor() as i64;
        // 二分查找第一个大于等于target的位置
        let i = {
            let mut left = 0;
            let mut right = psum.len().saturating_sub(1);
            let mut result = 0;

            while left <= right {
                let mid = left + (right - left) / 2;
                if psum[mid] < target {
                    result = mid;
                    left = mid + 1;
                } else {
                    right = mid.saturating_sub(1);
                }
            }
            result
        };

        // 获取对应的面积、宽度和高度
        let area = psum[i];
        let width = widths[i];
        let height = events[i].0;

        height as f64 + (total_area as f64 - area as f64 * 2.0) / (width as f64 * 2.0)
    }
}
```
