---
title: "leetcode-滑动窗口90"
date: 2026-07-18T11:02:34+08:00
tags: ["leetcode", "滑动窗口"]
draft: false
---


## 交替组 I

给你一个整数数组 colors ，它表示一个由红色和蓝色瓷砖组成的环，第 i 块瓷砖的颜色为 colors[i] ：

colors[i] == 0 表示第 i 块瓷砖的颜色是 红色 。
colors[i] == 1 表示第 i 块瓷砖的颜色是 蓝色 。

环中连续 3 块瓷砖的颜色如果是 交替 颜色（也就是说中间瓷砖的颜色与它 左边 和 右边 的颜色都不同），那么它被称为一个 交替 组。

请你返回 交替 组的数目。

注意 ，由于 colors 表示一个 环 ，第一块 瓷砖和 最后一块 瓷砖是相邻的。


```
impl Solution {
    pub fn number_of_alternating_groups(colors: Vec<i32>) -> i32 {
        let n = colors.len();
        let mut count = 0;

        // 检查每个位置作为中间瓷砖的交替组
        for i in 0..n {
            let prev = (i + n - 1) % n;
            let next = (i + 1) % n;
            // 中间瓷砖与左右两边都不同 => 形成交替
            if colors[i] != colors[prev] && colors[i] != colors[next] {
                count += 1;
            }
        }

        count
    }
}
```
