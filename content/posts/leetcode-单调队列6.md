---
title: "leetcode-单调队列6"
date: 2026-03-31T20:55:29+08:00
tags: ["leetcode", "单调队列"]
draft: false
---


## 满足不等式的最大值

给你一个数组 points 和一个整数 k 。数组中每个元素都表示二维平面上的点的坐标，并按照横坐标 x 的值从小到大排序。也就是说 points[i] = [xi, yi] ，并且在 1 <= i < j <= points.length 的前提下， xi < xj 总成立。

请你找出 yi + yj + |xi - xj| 的 最大值，其中 |xi - xj| <= k 且 1 <= i < j <= points.length。

题目测试数据保证至少存在一对能够满足 |xi - xj| <= k 的点。

```
use std::collections::VecDeque;

impl Solution {
    pub fn find_max_value_of_equation(points: Vec<Vec<i32>>, k: i32) -> i32 {
        let (ans, _) = points.iter().fold(
            (i32::MIN, VecDeque::<(i32, i32)>::new()),
            |(mut ans, mut dq), point| {
                let (px, py) = (point[0], point[1]);

                // 移除窗口外的点
                while dq.front().map_or(false, |&(x, _)| x < px - k) {
                    dq.pop_front();
                }

                // 更新答案
                if let Some(&(x, y)) = dq.front() {
                    ans = ans.max(px + py + y - x);
                }

                // 维护单调性
                while dq.back().map_or(false, |&(x, y)| py - px >= y - x) {
                    dq.pop_back();
                }

                dq.push_back((px, py));
                (ans, dq)
            },
        );
        ans
    }
}
```
