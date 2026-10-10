---
title: "leetcode-哈希表8"
date: 2026-10-09T20:07:55+08:00
tags: ["leetcode", "哈希表"]
draft: false
---


##  回旋镖的数量

给定平面上 n 对 互不相同 的点 points ，其中 points[i] = [xi, yi] 。回旋镖 是由点 (i, j, k) 表示的元组 ，其中 i 和 j 之间的欧式距离和 i 和 k 之间的欧式距离相等（需要考虑元组的顺序）。

返回平面上所有回旋镖的数量。


```
use std::collections::HashMap;

impl Solution {
    pub fn number_of_boomerangs(points: Vec<Vec<i32>>) -> i32 {
        // 对每个中心点 i，统计到其他点的距离平方出现次数。
        // 若某距离有 k 个点，则可组成 k*(k-1) 个有序回旋镖 (i,j,k)。
        // 遍历时边计数边累加 2*v（即新增 1 个点时额外贡献的组合数），
        // 等价于最终对每个距离累加 k*(k-1)，但无需二次遍历。
        let mut ans = 0;
        let mut cnt: HashMap<i32, i32> = HashMap::new();
        for p1 in &points {
            cnt.clear();
            for p2 in &points {
                let dx = p1[0] - p2[0];
                let dy = p1[1] - p2[1];
                let d2 = dx * dx + dy * dy;
                let v = cnt.entry(d2).or_insert(0);
                ans += *v * 2; // 新增点与已有 v 个点两两配对，有序所以 ×2
                *v += 1;
            }
        }
        ans
    }
}
```
