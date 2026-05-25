---
title: "leetcode-几何6"
date: 2026-05-11T20:20:55+08:00
tags: ["leetcode", "几何"]
draft: false
---


## 安装栅栏

给定一个数组 trees，其中 trees[i] = [xi, yi] 表示树在花园中的位置。

你被要求用最短长度的绳子把整个花园围起来，因为绳子很贵。只有把 所有的树都围起来，花园才围得很好。

返回恰好位于围栏周边的树木的坐标。

```
impl Solution {
    /// 返回所有位于凸包边界上的点（包含共线的边缘点）
    pub fn outer_trees(trees: Vec<Vec<i32>>) -> Vec<Vec<i32>> {
        let n = trees.len();
        if n < 4 {
            return trees; // 少于4个点，全部在边界上
        }

        // 转换为元组便于计算
        let mut points: Vec<(i32, i32)> = trees
            .into_iter()
            .map(|p| (p[0], p[1]))
            .collect();

        // 1. 找最下最左的点（y最小，y相同时x最小）
        let start_idx = points
            .iter()
            .enumerate()
            .min_by(|(_, p1), (_, p2)| {
                // p1, p2 已是 &(i32,i32)，直接比较字段
                p1.1.cmp(&p2.1).then_with(|| p1.0.cmp(&p2.0))
            })
            .map(|(i, _)| i)
            .unwrap();
        points.swap(0, start_idx);
        let start = points[0];

        // 2. 定义叉积和距离平方函数
        let cross = |p: &(i32, i32), q: &(i32, i32), r: &(i32, i32)| -> i32 {
            (q.0 - p.0) * (r.1 - q.1) - (q.1 - p.1) * (r.0 - q.0)
        };
        let dist2 = |p: &(i32, i32), q: &(i32, i32)| -> i32 {
            (p.0 - q.0).pow(2) + (p.1 - q.1).pow(2)
        };

        // 3. 极角排序（相对于 start）
        points[1..].sort_unstable_by(|a, b| {
            let diff = cross(&start, a, b);
            if diff != 0 {
                diff.cmp(&0).reverse() // 顺时针为正
            } else {
                dist2(&start, a).cmp(&dist2(&start, b))
            }
        });

        // 4. 反转尾部共线点（使凸包边界包含外侧点）
        let mut r = (n - 2) as i32;
        while r >= 0 && cross(&points[0], &points[n - 1], &points[r as usize]) == 0 {
            r -= 1;
        }
        let (mut i, mut j) = ((r + 1) as usize, n - 1);
        while i < j {
            points.swap(i, j);
            i += 1;
            j -= 1;
        }

        // 5. Graham Scan 构建凸包（使用函数式 fold 维护栈）
        let stack = (2..n).fold(vec![0, 1], |mut stack, i| {
            while stack.len() >= 2 {
                let a = stack[stack.len() - 2];
                let b = stack[stack.len() - 1];
                if cross(&points[a], &points[b], &points[i]) < 0 {
                    stack.pop();
                } else {
                    break;
                }
            }
            stack.push(i);
            stack
        });

        // 6. 转换回 Vec<Vec<i32>> 输出
        stack.into_iter().map(|i| vec![points[i].0, points[i].1]).collect()
    }
}
```
