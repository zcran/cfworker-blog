---
title: "leetcode-模拟23"
date: 2026-08-08T11:31:09+08:00
tags: ["leetcode", "模拟"]
draft: false
---


## 困于环中的机器人

在无限的平面上，机器人最初位于 (0, 0) 处，面朝北方。注意:

北方向 是y轴的正方向。
南方向 是y轴的负方向。
东方向 是x轴的正方向。
西方向 是x轴的负方向。

机器人可以接受下列三条指令之一：

"G"：直走 1 个单位
"L"：左转 90 度
"R"：右转 90 度

机器人按顺序执行指令 instructions，并一直重复它们。

只有在平面中存在环使得机器人永远无法离开时，返回 true。否则，返回 false。



```
impl Solution {
    /// 判断机器人是否被困在环中
    /// 关键数学结论：执行一轮指令后，若机器人回到原点，或方向不是朝北，
    /// 则最多执行4轮后必回到原点，即存在环。
    pub fn is_robot_bounded(instructions: String) -> bool {
        // 方向向量：北(0)、东(1)、南(2)、西(3)
        const DIRS: [(i32, i32); 4] = [(0, 1), (1, 0), (0, -1), (-1, 0)];

        let (mut x, mut y) = (0, 0);
        let mut dir = 0usize; // 0=北, 1=东, 2=南, 3=西

        for c in instructions.bytes() {
            match c {
                b'L' => dir = (dir + 3) & 3, // 左转 = 逆时针，+3 ≡ -1 (mod 4)
                b'R' => dir = (dir + 1) & 3, // 右转 = 顺时针，+1 (mod 4)
                _ => {
                    let (dx, dy) = DIRS[dir];
                    x += dx;
                    y += dy;
                }
            }
        }

        // 回到原点，或方向不是朝北（4轮后必回原点）
        (x == 0 && y == 0) || dir != 0
    }
}
```
