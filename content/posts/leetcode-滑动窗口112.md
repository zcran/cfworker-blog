---
title: "leetcode-滑动窗口112"
date: 2026-07-18T11:02:35+08:00
tags: ["leetcode", "滑动窗口"]
draft: false
---


## 删除子字符串后不同的终点

给你一个由字符 'U'、'D'、'L' 和 'R' 组成的字符串 s，表示在无限的二维笛卡尔网格上的移动。

'U': 从 (x, y) 移动到 (x, y + 1)。
'D': 从 (x, y) 移动到 (x, y - 1)。
'L': 从 (x, y) 移动到 (x - 1, y)。
'R': 从 (x, y) 移动到 (x + 1, y)。

你还得到了一个正整数 k。

你 必须 选择并移除 恰好一个 长度为 k 的连续子字符串 s。然后，从坐标 (0, 0) 开始，按顺序执行剩余的移动。

返回可到达的 不同 最终坐标的数量。


```
use std::collections::HashSet;

impl Solution {
    pub fn distinct_points(s: String, k: i32) -> i32 {
        let n = s.len();
        let k = k as usize;
        let bytes = s.as_bytes();

        // 方向映射表：直接用 ASCII 码索引，O(1) 查表
        const DIR: [(i32, i32); 128] = {
            let mut map = [(0, 0); 128];
            map[b'L' as usize] = (-1, 0);
            map[b'R' as usize] = (1, 0);
            map[b'D' as usize] = (0, -1);
            map[b'U' as usize] = (0, 1);
            map
        };

        let mut points = HashSet::with_capacity(n - k + 1);
        let (mut x, mut y) = (0, 0);

        // 初始窗口 [0, k-1]
        for &ch in &bytes[..k] {
            let (dx, dy) = DIR[ch as usize];
            x += dx;
            y += dy;
        }
        points.insert((x, y));

        // 滑动窗口：每次向右移动一位
        for i in k..n {
            let (add_x, add_y) = DIR[bytes[i] as usize];          // 进入窗口
            let (sub_x, sub_y) = DIR[bytes[i - k] as usize];       // 离开窗口
            x += add_x - sub_x;
            y += add_y - sub_y;
            points.insert((x, y));
        }

        points.len() as i32
    }
}
```
