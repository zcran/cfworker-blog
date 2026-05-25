---
title: "leetcode-几何3"
date: 2026-05-11T20:20:55+08:00
tags: ["leetcode", "几何"]
draft: false
---


## 路径交叉

给你一个整数数组 distance 。

从 X-Y 平面上的点 (0,0) 开始，先向北移动 distance[0] 米，然后向西移动 distance[1] 米，向南移动 distance[2] 米，向东移动 distance[3] 米，持续移动。也就是说，每次移动后你的方位会发生逆时针变化。

判断你所经过的路径是否相交。如果相交，返回 true ；否则，返回 false 。

```
impl Solution {
    pub fn is_self_crossing(distance: Vec<i32>) -> bool {
        let d = distance;
        let n = d.len();

        if n < 4 {
            return false;
        }

        // 定义三个模式匹配谓词，均接受索引 i
        let cross3 = |i: usize| d[i] >= d[i - 2] && d[i - 1] <= d[i - 3];
        let cross4 = |i: usize| d[i] + d[i - 4] >= d[i - 2] && d[i - 1] == d[i - 3];
        let cross5 = |i: usize| {
            d[i] + d[i - 4] >= d[i - 2] &&
            d[i - 1] <= d[i - 3] &&
            d[i - 1] + d[i - 5] >= d[i - 3] &&
            d[i - 2] >= d[i - 4]
        };

        // 构建待检查的索引序列，然后使用 any 短路求值
        (3..n).any(|i| match i {
            3 => cross3(3),
            4 if n >= 5 => cross3(4) || cross4(4),
            i if i >= 5 => cross3(i) || cross4(i) || cross5(i),
            _ => false,
        })
    }
}
```
