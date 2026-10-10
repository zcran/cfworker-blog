---
title: "leetcode-哈希表52"
date: 2026-10-09T20:07:58+08:00
tags: ["leetcode", "哈希表"]
draft: false
---


## 环和杆

总计有 n 个环，环的颜色可以是红、绿、蓝中的一种。这些环分别穿在 10 根编号为 0 到 9 的杆上。

给你一个长度为 2n 的字符串 rings ，表示这 n 个环在杆上的分布。rings 中每两个字符形成一个 颜色位置对 ，用于描述每个环：

第 i 对中的 第一个 字符表示第 i 个环的 颜色（'R'、'G'、'B'）。
第 i 对中的 第二个 字符表示第 i 个环的 位置，也就是位于哪根杆上（'0' 到 '9'）。

例如，"R3G2B1" 表示：共有 n == 3 个环，红色的环在编号为 3 的杆上，绿色的环在编号为 2 的杆上，蓝色的环在编号为 1 的杆上。

找出所有集齐 全部三种颜色 环的杆，并返回这种杆的数量。


```
impl Solution {
    pub fn count_points(rings: String) -> i32 {
        // 用位掩码表示每根杆上出现的颜色：R=1, G=2, B=4
        let mut mask = [0u8; 10];
        let bytes = rings.as_bytes();

        // rings 每两个字节构成一个颜色-位置对
        for pair in bytes.chunks_exact(2) {
            let color_bit = match pair[0] {
                b'R' => 1,
                b'G' => 2,
                b'B' => 4,
                _ => continue,
            };
            let rod = (pair[1] - b'0') as usize;
            mask[rod] |= color_bit;
        }

        // 三种颜色齐全 == 掩码为 0b111 == 7
        mask.iter().filter(|&&m| m == 7).count() as i32
    }
}
```
