---
title: "leetcode-位运算20"
date: 2026-09-24T10:33:35+08:00
tags: ["leetcode", "位运算"]
draft: false
---


## 二进制间距

给定一个正整数 n，找到并返回 n 的二进制表示中两个 相邻 1 之间的 最长距离 。如果不存在两个相邻的 1，返回 0 。

如果只有 0 将两个 1 分隔开（可能不存在 0 ），则认为这两个 1 彼此 相邻 。两个 1 之间的距离是它们的二进制表示中位置的绝对差。例如，"1001" 中的两个 1 的距离为 3 。


```
impl Solution {
    /// 核心思路：
    /// 利用 `n &= n - 1` 不断去掉最低位的 1，同时用 `trailing_zeros` 获取其位置。
    /// 相邻两个 1 的距离 = 当前 1 的位置 - 上一个 1 的位置。
    ///
    /// 先处理最低位的 1 作为起点，然后循环处理剩余的 1，更新最大距离。
    /// 时间复杂度 O(1)（最多 32 位），空间 O(1)。
    pub fn binary_gap(mut n: i32) -> i32 {
        // 最低位的 1 的位置
        let mut last = n.trailing_zeros();
        // 去掉最低位的 1
        n &= n - 1;

        let mut max_gap = 0;
        while n > 0 {
            // 当前最低位 1 的位置
            let pos = n.trailing_zeros();
            max_gap = max_gap.max(pos - last);
            last = pos;
            // 去掉当前最低位的 1
            n &= n - 1;
        }

        max_gap as i32
    }
}
```
