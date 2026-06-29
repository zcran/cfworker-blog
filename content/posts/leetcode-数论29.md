---
title: "leetcode-数论29"
date: 2026-06-20T11:09:56+08:00
tags: ["leetcode", "数论"]
draft: false
---


## 判断一个点是否可以到达

给你一个无穷大的网格图。一开始你在 (1, 1) ，你需要通过有限步移动到达点 (targetX, targetY) 。

每一步 ，你可以从点 (x, y) 移动到以下点之一：

· (x, y - x)
· (x - y, y)
· (2 * x, y)
· (x, 2 * y)

给你两个整数 targetX 和 targetY ，分别表示你最后需要到达点的 X 和 Y 坐标。如果你可以从 (1, 1) 出发到达这个点，请你返回true ，否则返回 false 。


```
impl Solution {
    /// 判断能否从 (1,1) 通过四种操作到达 (target_x, target_y)
    ///
    /// 核心思路：逆向思考，从目标点反向操作回到 (1,1)
    /// 反向操作：如果 x,y 都是偶数则除以2，否则用较大的数对较小的数取模
    /// 最终如果能得到 (1,1) 则可达
    pub fn is_reachable(target_x: i32, target_y: i32) -> bool {
        let mut x = target_x;
        let mut y = target_y;

        // 逆向操作：同时约去公共因子2
        while x % 2 == 0 && y % 2 == 0 {
            x /= 2;
            y /= 2;
        }

        // 使用欧几里得算法求最大公约数
        // 实际上检查 gcd(x,y) 是否为 2 的幂
        let g = gcd(x, y);

        // 判断 g 是否只有因子2（即是否为2的幂）
        g & (g - 1) == 0
    }
}

/// 欧几里得算法求最大公约数
fn gcd(mut a: i32, mut b: i32) -> i32 {
    while b != 0 {
        let temp = b;
        b = a % b;
        a = temp;
    }
    a
}
```
