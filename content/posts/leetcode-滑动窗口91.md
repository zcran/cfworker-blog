---
title: "leetcode-滑动窗口91"
date: 2026-07-18T11:02:34+08:00
tags: ["leetcode", "滑动窗口"]
draft: false
---


## 交替组 II

给你一个整数数组 colors 和一个整数 k ，colors表示一个由红色和蓝色瓷砖组成的环，第 i 块瓷砖的颜色为 colors[i] ：

colors[i] == 0 表示第 i 块瓷砖的颜色是 红色 。
colors[i] == 1 表示第 i 块瓷砖的颜色是 蓝色 。
环中连续 k 块瓷砖的颜色如果是 交替 颜色（也就是说除了第一块和最后一块瓷砖以外，中间瓷砖的颜色与它 左边 和 右边 的颜色都不同），那么它被称为一个 交替 组。

请你返回 交替 组的数目。

注意 ，由于 colors 表示一个 环 ，第一块 瓷砖和 最后一块 瓷砖是相邻的。


```
impl Solution {
    pub fn number_of_alternating_groups(colors: Vec<i32>, k: i32) -> i32 {
        let n = colors.len() as i32;
        let mut alternating_groups = 0;
        // 记录当前连续交替序列的长度
        let mut alternating_len = 1;

        // 从索引 (-k + 2) 开始遍历到 n-1
        // 这确保我们考虑了所有以位置 i 为结尾的长度为 k 的环形子数组
        for i in (-k + 2)..n {
            let current = ((i + n) % n) as usize;
            let previous = ((i - 1 + n) % n) as usize;

            // 如果当前瓷砖与前一个颜色不同，交替序列继续延长
            if colors[current] != colors[previous] {
                alternating_len += 1;
            } else {
                // 否则交替中断，从当前瓷砖重新开始计数
                alternating_len = 1;
            }

            // 当连续交替长度达到或超过 k 时，找到一个有效的交替组
            if alternating_len >= k {
                alternating_groups += 1;
            }
        }

        alternating_groups
    }
}
```
