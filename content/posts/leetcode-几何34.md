---
title: "leetcode-几何34"
date: 2026-05-11T20:20:56+08:00
tags: ["leetcode", "几何"]
draft: false
---


## 游乐园的迷宫

小王来到了游乐园，她玩的第一个项目是模拟推销员。有一个二维平面地图，其中散布着 N 个推销点，编号 0 到 N-1，不存在三点共线的情况。每两点之间有一条直线相连。游戏没有规定起点和终点，但限定了每次转角的方向。首先，小王需要先选择两个点分别作为起点和终点，然后从起点开始访问剩余 N-2 个点恰好一次并回到终点。访问的顺序需要满足一串给定的长度为 N-2 由 L 和 R 组成的字符串 direction，表示从起点出发之后在每个顶点上转角的方向。根据这个提示，小王希望你能够帮她找到一个可行的遍历顺序，输出顺序下标（若有多个方案，输出任意一种）。可以证明这样的遍历顺序一定是存在的。


```
impl Solution {
    /// 根据方向字符串访问所有点。
    /// 从最左下的点开始，每一步选择相对于当前点最“右”（'L'）或最“左”（'R'）的剩余点。
    /// 返回访问顺序的索引。
    pub fn visit_order(points: Vec<Vec<i32>>, dir: String) -> Vec<i32> {
        let n = points.len();
        // 转换为 i64 避免叉积溢出
        let pts: Vec<(i64, i64)> = points
            .into_iter()
            .map(|p| (p[0] as i64, p[1] as i64))
            .collect();

        let mut used = vec![false; n];
        let mut order = Vec::with_capacity(n);

        // 起点：最左下的点（x 最小，x 相同时 y 最小）
        let start = (0..n).min_by_key(|&i| pts[i]).unwrap();
        used[start] = true;
        order.push(start as i32);
        let mut cur = start;

        for ch in dir.chars() {
            // 在所有未使用的点中，根据方向选择极角最极端的点
            let next = (0..n)
                .filter(|&j| !used[j])
                .fold(None, |best: Option<usize>, j| {
                    if let Some(b) = best {
                        // 向量 cur->b 和 cur->j
                        let (bx, by) = (pts[b].0 - pts[cur].0, pts[b].1 - pts[cur].1);
                        let (jx, jy) = (pts[j].0 - pts[cur].0, pts[j].1 - pts[cur].1);
                        let cross = bx * jy - by * jx; // 二维叉积
                        let better = match ch {
                            'L' => cross < 0, // 顺时针（右转）
                            'R' => cross > 0, // 逆时针（左转）
                            _ => false,
                        };
                        if better { Some(j) } else { Some(b) }
                    } else {
                        Some(j)
                    }
                })
                .unwrap();
            used[next] = true;
            order.push(next as i32);
            cur = next;
        }

        // 添加最后一个未访问的点
        if let Some(last) = (0..n).find(|&i| !used[i]) {
            order.push(last as i32);
        }

        order
    }
}
```
