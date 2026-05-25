---
title: "leetcode-几何40"
date: 2026-05-11T20:20:56+08:00
tags: ["leetcode", "几何"]
draft: false
---

## 最佳直线


给定一个二维平面及平面上的 N 个点列表Points，其中第i个点的坐标为Points[i]=[Xi,Yi]。请找出一条直线，其通过的点的数目最多。

设穿过最多点的直线所穿过的全部点编号从小到大排序的列表为S，你仅需返回[S[0],S[1]]作为答案，若有多条直线穿过了相同数量的点，则选择S[0]值较小的直线返回，S[0]相同则选择S[1]值较小的直线返回。

```
impl Solution {
    /// 返回经过最多点的直线上的两个点下标（按下标升序）。
    /// 若有多条直线，返回下标字典序最小的点对。
    pub fn best_line(points: Vec<Vec<i32>>) -> Vec<i32> {
        let n = points.len();
        // 转换为元组以便快速访问
        let pts: Vec<(i32, i32)> = points.iter().map(|p| (p[0], p[1])).collect();
        let mut best = (0, 0);
        let mut max_cnt = 0;

        for i in 0..n {
            let (x1, y1) = pts[i];
            for j in i + 1..n {
                let (x2, y2) = pts[j];
                let dx = x2 - x1;
                let dy = y2 - y1;
                let mut cnt = 2; // 至少包含 i 和 j
                // 剪枝：剩余点数不足以超过当前最优解
                let remaining = n - j - 1;
                if max_cnt >= cnt + remaining {
                    break;
                }
                // 统计其他共线的点
                cnt += (j + 1..n)
                    .filter(|&k| {
                        let (x3, y3) = pts[k];
                        let dx2 = x3 - x1;
                        let dy2 = y3 - y1;
                        dx * dy2 == dy * dx2
                    })
                    .count();
                if cnt > max_cnt {
                    max_cnt = cnt;
                    best = (i, j);
                }
            }
        }
        vec![best.0 as i32, best.1 as i32]
    }
}
```
