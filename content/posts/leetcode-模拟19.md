---
title: "leetcode-模拟19"
date: 2026-08-08T11:31:09+08:00
tags: ["leetcode", "模拟"]
draft: false
---


## 模拟行走机器人

机器人在一个无限大小的 XY 网格平面上行走，从点 (0, 0) 处开始出发，面向北方。该机器人可以接收以下三种类型的命令 commands ：

-2 ：向左转 90 度
-1 ：向右转 90 度
1 <= x <= 9 ：向前移动 x 个单位长度
在网格上有一些格子被视为障碍物 obstacles 。第 i 个障碍物位于网格点  obstacles[i] = (xi, yi) 。

机器人无法走到障碍物上，它将会停留在障碍物的前一个网格方块上，并继续执行下一个命令。

返回机器人距离原点的 最大欧式距离 的 平方 。（即，如果距离为 5 ，则返回 25 ）


注意：

北方表示 +Y 方向。
东方表示 +X 方向。
南方表示 -Y 方向。
西方表示 -X 方向。
原点 [0,0] 可能会有障碍物。


```
use std::collections::HashSet;

impl Solution {
    /// 模拟机器人行走，返回最大欧式距离平方
    /// 方向编码: 0=北(+Y), 1=东(+X), 2=南(-Y), 3=西(-X)
    /// 障碍物哈希: 将二维坐标编码为单一 i64，避免元组哈希开销
    pub fn robot_sim(commands: Vec<i32>, obstacles: Vec<Vec<i32>>) -> i32 {
        // 方向向量：北、东、南、西（顺时针）
        const DIRS: [(i32, i32); 4] = [(0, 1), (1, 0), (0, -1), (-1, 0)];
        // 坐标编码偏移量，需大于坐标范围 [-30000, 30000]
        const OFFSET: i64 = 60_001;

        let mut x = 0i32;
        let mut y = 0i32;
        let mut dir = 0usize;

        // 障碍物集合：编码为 x * OFFSET + y，用 i64 避免乘法溢出
        let obstacles: HashSet<i64> = obstacles
            .into_iter()
            .map(|o| o[0] as i64 * OFFSET + o[1] as i64)
            .collect();

        let mut max_dist = 0;

        for cmd in commands {
            match cmd {
                -2 => dir = (dir + 3) & 3,      // 左转: -1 ≡ +3 (mod 4)
                -1 => dir = (dir + 1) & 3,      // 右转: +1 (mod 4)
                step => {
                    let (dx, dy) = DIRS[dir];
                    for _ in 0..step {
                        let nx = x + dx;
                        let ny = y + dy;
                        // 检查前方是否为障碍物
                        if obstacles.contains(&(nx as i64 * OFFSET + ny as i64)) {
                            break;
                        }
                        x = nx;
                        y = ny;
                        max_dist = max_dist.max(x * x + y * y);
                    }
                }
            }
        }

        max_dist
    }
}
```
