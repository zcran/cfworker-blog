---
title: "leetcode-位运算99"
date: 2026-09-24T10:33:40+08:00
tags: ["leetcode", "位运算"]
draft: false
---


## 绘制直线

已知一个由像素点组成的单色屏幕，每行均有 w 个像素点，所有像素点初始为 0，左上角位置为 (0,0)。

现将每行的像素点按照「每 32 个像素点」为一组存放在一个 int 中，再依次存入长度为 length 的一维数组中。

我们将在屏幕上绘制一条从点 (x1,y) 到点 (x2,y) 的直线（即像素点修改为 1），请返回绘制过后的数组。

```
impl Solution {
    pub fn draw_line(length: i32, w: i32, x1: i32, x2: i32, y: i32) -> Vec<i32> {
        // 屏幕每行 w 个像素，按每 32 像素一组存入 int（高位对应左侧像素）。
        // 第 y 行的起始 int 下标为 y * w / 32。
        //
        // 关键点：像素 x 位于其所在 int 的第 (31 - x % 32) 位（高位在左）。
        // 直线横跨 [x1, x2]，可能覆盖多个 int：
        //   - 中间完整的 int 全为 -1
        //   - 两端各按位设置对应区间的位

        let mut ans = vec![0i32; length as usize];
        let base = y * w / 32;                 // 该行第一个 int 的下标
        let (start, end) = (base + x1 / 32, base + x2 / 32);

        // 中间整块直接置全 1（仅当确实存在至少一个完整中间块时）
        if end > start + 1 {
            for v in ans[(start + 1) as usize..end as usize].iter_mut() {
                *v = -1;
            }
        }

        // 左端：从 bit (31 - x1%32) 一路到 bit 0
        let left_bit = 31 - x1 % 32;
        // 右端：从 bit 31 一路到 bit (31 - x2%32)
        let right_bit = 31 - x2 % 32;

        if start == end {
            // 同一 int 内：填充 [right_bit, left_bit]
            for i in right_bit..=left_bit {
                ans[start as usize] |= 1 << i;
            }
        } else {
            // 左端 int：填充 [0, left_bit]
            for i in 0..=left_bit {
                ans[start as usize] |= 1 << i;
            }
            // 右端 int：填充 [right_bit, 31]
            for i in right_bit..32 {
                ans[end as usize] |= 1 << i;
            }
        }

        ans
    }
}
```
