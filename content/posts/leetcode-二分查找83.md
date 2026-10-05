---
title: "leetcode-二分查找83"
date: 2026-09-28T12:29:16+08:00
tags: ["leetcode", "二分查找"]
draft: false
---


## 可以被机器人摧毁的最大墙壁数目

一条无限长的直线上分布着一些机器人和墙壁。给你整数数组 robots ，distance 和 walls：

· robots[i] 是第 i 个机器人的位置。
· distance[i] 是第 i 个机器人的子弹可以行进的 最大 距离。
· walls[j] 是第 j 堵墙的位置。

每个机器人有 一颗 子弹，可以向左或向右发射，最远距离为 distance[i] 米。

子弹会摧毁其射程内路径上的每一堵墙。机器人是固定的障碍物：如果子弹在到达墙壁前击中另一个机器人，它会 立即 在该机器人处停止，无法继续前进。

返回机器人可以摧毁墙壁的 最大 数量。

注意：

墙壁和机器人可能在同一位置；该位置的墙壁可以被该位置的机器人摧毁。
机器人不会被子弹摧毁。


```
impl Solution {
    pub fn max_walls(robots: Vec<i32>, distance: Vec<i32>, walls: Vec<i32>) -> i32 {
        let n = robots.len();

        // 机器人位置 -> 射程
        let mut dist_map = std::collections::HashMap::new();
        for i in 0..n {
            dist_map.insert(robots[i], distance[i]);
        }

        // 按位置排序机器人
        let mut r = robots.clone();
        r.sort_unstable();
        let mut w = walls.clone();
        w.sort_unstable();

        // left[i]：机器人 i 向左能摧毁的墙数（不越过前一个机器人）
        // right[i]：机器人 i 向右能摧毁的墙数（不越过后一个机器人）
        // num[i]：机器人 i-1 与 i 之间的墙数
        let mut left = vec![0_i32; n];
        let mut right = vec![0_i32; n];
        let mut num = vec![0_i32; n];

        for i in 0..n {
            let d = dist_map[&r[i]];
            let pos = w.partition_point(|&x| x <= r[i]); // 第一个 > r[i] 的位置

            // 向左的边界：不能越过前一个机器人
            let left_bound = if i >= 1 {
                (r[i] - d).max(r[i - 1] + 1)
            } else {
                r[i] - d
            };
            left[i] = (pos - w.partition_point(|&x| x < left_bound)) as i32;

            // 向右的边界：不能越过后一个机器人
            let right_bound = if i < n - 1 {
                (r[i] + d).min(r[i + 1] - 1)
            } else {
                r[i] + d
            };
            right[i] = (w.partition_point(|&x| x <= right_bound)
                - w.partition_point(|&x| x < r[i])) as i32;

            if i >= 1 {
                num[i] = (pos - w.partition_point(|&x| x < r[i - 1])) as i32;
            }
        }

        // DP：sub_left / sub_right 分别表示处理到当前机器人时，
        // 当前机器人向左 / 向右射击所能摧毁的最大墙数
        let mut sub_left = left[0];
        let mut sub_right = right[0];

        for i in 1..n {
            let cur_left = (sub_left + left[i])
                .max(sub_right - right[i - 1] + (left[i] + right[i - 1]).min(num[i]));
            let cur_right = (sub_left + right[i]).max(sub_right + right[i]);
            sub_left = cur_left;
            sub_right = cur_right;
        }

        sub_left.max(sub_right)
    }
}
```
