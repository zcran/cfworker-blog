---
title: "leetcode-滑动窗口58"
date: 2026-07-18T11:02:32+08:00
tags: ["leetcode", "滑动窗口"]
draft: false
---


## 得到 K 个黑块的最少涂色次数

给你一个长度为 n 下标从 0 开始的字符串 blocks ，blocks[i] 要么是 'W' 要么是 'B' ，表示第 i 块的颜色。字符 'W' 和 'B' 分别表示白色和黑色。

给你一个整数 k ，表示想要 连续 黑色块的数目。

每一次操作中，你可以选择一个白色块将它 涂成 黑色块。

请你返回至少出现 一次 连续 k 个黑色块的 最少 操作次数。


```
impl Solution {
    pub fn minimum_recolors(blocks: String, k: i32) -> i32 {
        let k = k as usize;
        let bytes = blocks.as_bytes();
        let n = bytes.len();

        // 如果长度不足k，返回0（实际上这种情况不会发生）
        if n < k {
            return 0;
        }

        // 统计第一个窗口中的白色块数量
        let mut white_count = bytes[0..k].iter().filter(|&&b| b == b'W').count() as i32;
        let mut min_ops = white_count;

        // 滑动窗口，每次移除一个字符并加入一个新字符
        for i in k..n {
            // 移除离开窗口的字符
            if bytes[i - k] == b'W' {
                white_count -= 1;
            }
            // 加入进入窗口的字符
            if bytes[i] == b'W' {
                white_count += 1;
            }
            // 更新最小操作次数
            min_ops = min_ops.min(white_count);
        }

        min_ops
    }
}
```
