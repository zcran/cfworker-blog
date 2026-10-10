---
title: "leetcode-动态规划77"
date: 2026-10-05T20:39:59+08:00
tags: ["leetcode", "动态规划"]
draft: false
---


## 二指输入的的最小距离

A   B   C   D   E   F

G   H   I   J   K   L

M   N   O   P   Q   R

S   T   U   V   W   X

Y   Z

二指输入法定制键盘在 X-Y 平面上的布局如上所示，其中每个大写英文字母都位于某个坐标处。

例如字母 A 位于坐标 (0,0)，字母 B 位于坐标 (0,1)，字母 P 位于坐标 (2,3) 且字母 Z 位于坐标 (4,1)。
给你一个待输入字符串 word，请你计算并返回在仅使用两根手指的情况下，键入该字符串需要的最小移动总距离。

坐标 (x1,y1) 和 (x2,y2) 之间的 距离 是 |x1 - x2| + |y1 - y2|。

注意，两根手指的起始位置是零代价的，不计入移动总距离。你的两根手指的起始位置也不必从首字母或者前两个字母开始。


```
impl Solution {
    /// 1320. 二指输入的的最小距离
    ///
    /// 键盘布局：6 列 5 行（最后一行仅 Y、Z）。
    /// 把字母编号 0..26，坐标 = (编号 / 6, 编号 % 6)。
    ///
    /// dp[i][j][k]：打完前 i 个字母后，两根手指分别停在字母 j、k 上的最小距离。
    /// 官方解法状态为 26×26，每步更新时还要额外枚举 k，复杂度 O(n·26^3)。
    ///
    /// 优化：注意到每打完一个字母 cur，必有一根手指停在 cur 上，
    /// 因此状态可压缩为 dp[other]：另一根手指停在哪，当前手指停在 cur。
    ///   - 用当前手指敲下一个字母 next：
    ///       dp'[other] = min(dp'[other], dp[other] + dist(cur, next))
    ///   - 用另一根手指敲 next（另一根手指从 other 移到 next）：
    ///       dp'[cur] = min(dp'[cur], dp[other] + dist(other, next))
    ///
    /// 起始：两个手指初始位置任意，代价 0。可视为第一枚字母由"某根手指"点击，
    /// 另一根手指停留在任意位置，代价 0，因此 dp[other] 初值均为 0。
    ///
    /// 时间 O(n·26)，空间 O(26)。
    pub fn minimum_distance(word: String) -> i32 {
        const INF: i32 = i32::MAX / 2;
        let bytes = word.as_bytes();

        // 坐标
        let pos = |c: u8| -> (i32, i32) {
            let v = (c - b'A') as i32;
            (v / 6, v % 6)
        };
        let dist = |a: u8, b: u8| -> i32 {
            let (x1, y1) = pos(a);
            let (x2, y2) = pos(b);
            (x1 - x2).abs() + (y1 - y2).abs()
        };

        // dp[other]：当前手指停在上一字母处，另一根手指停在 other 字母处的最小距离
        let mut dp = [0i32; 26];
        let mut prev = bytes[0];

        for &cur in &bytes[1..] {
            let mut next = [INF; 26];
            for other in 0..26 {
                let other_ch = b'A' + other as u8;
                let base = dp[other];

                // 1) 用"当前手指"敲 cur：另一根手指不动
                let d1 = base + dist(prev, cur);
                if d1 < next[other] {
                    next[other] = d1;
                }

                // 2) 用"另一根手指"从 other 敲 cur：当前手指停在 prev
                let d2 = base + dist(other_ch, cur);
                let idx = (prev - b'A') as usize;
                if d2 < next[idx] {
                    next[idx] = d2;
                }
            }
            dp = next;
            prev = cur;
        }

        dp.iter().copied().min().unwrap()
    }
}
```
