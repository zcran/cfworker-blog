---
title: "leetcode-设计27"
date: 2026-07-14T10:41:26+08:00
tags: ["leetcode", "设计"]
draft: false
---


## 模拟行走机器人 II

给你一个在 XY 平面上的 width x height 的网格图，左下角 的格子为 (0, 0) ，右上角 的格子为 (width - 1, height - 1) 。网格图中相邻格子为四个基本方向之一（"North"，"East"，"South" 和 "West"）。一个机器人 初始 在格子 (0, 0) ，方向为 "East" 。

机器人可以根据指令移动指定的 步数 。每一步，它可以执行以下操作。

1. 沿着当前方向尝试 往前一步 。
2. 如果机器人下一步将到达的格子 超出了边界 ，机器人会 逆时针 转 90 度，然后再尝试往前一步。

如果机器人完成了指令要求的移动步数，它将停止移动并等待下一个指令。

请你实现 Robot 类：

· Robot(int width, int height) 初始化一个 width x height 的网格图，机器人初始在 (0, 0) ，方向朝 "East" 。
· void step(int num) 给机器人下达前进 num 步的指令。
· int[] getPos() 返回机器人当前所处的格子位置，用一个长度为 2 的数组 [x, y] 表示。
· String getDir() 返回当前机器人的朝向，为 "North" ，"East" ，"South" 或者 "West" 。


```
/// 网格边界机器人
///
/// 利用边界行走的周期性，将步数取模，实现 O(1) 移动
/// 时间复杂度: step O(1), getPos O(1), getDir O(1)
/// 空间复杂度: O(1)
struct Robot {
    width: i32,   // 网格宽度
    height: i32,  // 网格高度
    perimeter: i32, // 边界总步长（走一圈回到原点的步数）
    offset: i32,   // 当前位置在边界上的偏移量 [1, perimeter]
}

impl Robot {
    /// 初始化机器人，起点 (0,0)，朝东
    fn new(width: i32, height: i32) -> Self {
        let perimeter = 2 * (width + height - 2);
        Robot {
            width,
            height,
            perimeter,
            offset: 0,
        }
    }

    /// 前进 num 步
    ///
    /// 将偏移量映射到 [1, perimeter] 范围，避免 offset=0 时的朝向歧义
    fn step(&mut self, num: i32) {
        // 取模前先减 1，映射到 0..perimeter-1 再取模，最后加 1 回到 1..perimeter
        self.offset = (self.offset + num - 1) % self.perimeter + 1;
    }

    /// 根据偏移量计算当前位置和朝向
    fn get_state(&self) -> (i32, i32, &'static str) {
        let w = self.width;
        let h = self.height;
        let s = self.offset;

        // 边界路径：底边 → 右边 → 顶边 → 左边（逆时针）
        if s < w {
            // 底边：从左到右，朝东
            (s, 0, "East")
        } else if s < w + h - 1 {
            // 右边：从下到上，朝北
            (w - 1, s - w + 1, "North")
        } else if s < 2 * w + h - 2 {
            // 顶边：从右到左，朝西
            (2 * w + h - s - 3, h - 1, "West")
        } else {
            // 左边：从上到下，朝南
            (0, 2 * (w + h) - s - 4, "South")
        }
    }

    /// 获取当前位置 [x, y]
    fn get_pos(&self) -> Vec<i32> {
        let (x, y, _) = self.get_state();
        vec![x, y]
    }

    /// 获取当前朝向
    fn get_dir(&self) -> String {
        let (_, _, dir) = self.get_state();
        dir.to_string()
    }
}
```
