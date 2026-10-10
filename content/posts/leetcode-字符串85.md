---
title: "leetcode-字符串85"
date: 2026-10-10T12:56:58+08:00
tags: ["leetcode", "字符串"]
draft: false
---


## 恰好有 K 对相等相邻字符的循环移位数量

给你一个长度为 n 的字符串 s 和一个整数 k。

s 的一次 循环移位 可以通过以下方式得到：选择 s 的一个长度在 0 到 n - 1（包含两端）之间的 前缀 ，并将其移动到字符串末尾，同时保持所有字符的相对顺序不变。

对于 s 的 每一种 循环移位，定义其 得分 为满足以下条件的下标 i 的数量：0 <= i < n - 1，且位置 i 和 i + 1 处的字符相同。

返回得分等于 k 的循环移位数量。

字符串的 前缀 是指从字符串开头开始，并延伸到字符串中某个位置的子串。

子串 是字符串中一段连续的字符序列，可以为空。


```
impl Solution {
    pub fn count_rotations(s: String, k: i32) -> i32 {
        let b = s.as_bytes();
        let n = b.len();
        let k = k as usize;

        // 每个循环移位的得分 = 该移位中相邻相同字符的对数
        // 用滑动窗口统计长度为 n-1 的相邻对（即去掉首尾相连的那一对）
        let mut same = 0usize;
        let mut ans = 0;

        // 遍历 2n-2 个相邻对，窗口大小为 n-1
        for i in 0..(2 * n - 2) {
            // 入窗口
            if b[i % n] == b[(i + 1) % n] {
                same += 1;
            }

            let left = i as isize - n as isize + 2;
            if left < 0 {
                continue;
            }

            // 窗口已满，判断得分
            if same == k {
                ans += 1;
            }

            // 出窗口
            let l = left as usize;
            if b[l] == b[(l + 1) % n] {
                same -= 1;
            }
        }

        ans
    }
}
```
