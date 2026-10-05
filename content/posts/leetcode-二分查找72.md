---
title: "leetcode-二分查找72"
date: 2026-09-28T12:29:15+08:00
tags: ["leetcode", "二分查找"]
draft: false
---


## 正方形中的最多点数

给你一个二维数组 points 和一个字符串 s ，其中 points[i] 表示第 i 个点的坐标，s[i] 表示第 i 个点的 标签 。

如果一个正方形的中心在 (0, 0) ，所有边都平行于坐标轴，且正方形内 不 存在标签相同的两个点，那么我们称这个正方形是 合法 的。

请你返回 合法 正方形中可以包含的 最多 点数。

注意：

如果一个点位于正方形的边上或者在边以内，则认为该点位于正方形内。
正方形的边长可以为零。


```
impl Solution {
    pub fn max_points_inside_square(points: Vec<Vec<i32>>, s: String) -> i32 {
        // min1[c] 记录标签 c 对应点中切比雪夫距离最小的值
        // min2 记录所有标签中第二小的距离（用于确定正方形边长上界）
        let mut min1 = [i32::MAX; 26];
        let mut min2 = i32::MAX;

        for (i, ch) in s.bytes().enumerate() {
            let idx = (ch - b'a') as usize;
            let d = points[i][0].abs().max(points[i][1].abs());
            if d < min1[idx] {
                // 更新该标签的最小值，旧值参与第二小值的竞争
                min2 = min2.min(min1[idx]);
                min1[idx] = d;
            } else if d < min2 {
                min2 = d;
            }
        }

        // 边长受限于第二小的距离：只有最小距离严格小于 min2 的标签才能被包含
        min1.iter().filter(|&&d| d < min2).count() as i32
    }
}
```
