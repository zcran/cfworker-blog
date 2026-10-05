---
title: "leetcode-广度优先搜索4"
date: 2026-09-13T09:32:06+08:00
tags: ["leetcode", "广度优先搜索"]
draft: false
---


## 水壶问题

有两个水壶，容量分别为 x 和 y 升。水的供应是无限的。确定是否有可能使用这两个壶准确得到 target 升。

你可以：

装满任意一个水壶
清空任意一个水壶
将水从一个水壶倒入另一个水壶，直到接水壶已满，或倒水壶已空。


```
impl Solution {
    /// 数论解法（贝祖定理）：可行当且仅当 target 不超过总容量，且是 gcd(x, y) 的倍数。
    pub fn can_measure_water(x: i32, y: i32, t: i32) -> bool {
        // 两个水壶的总水量不可能超过 x + y
        if x + y < t {
            return false;
        }
        // 能量出的水量一定是 gcd(x, y) 的倍数（包含 t = 0 的情况）
        t % Self::gcd(x, y) == 0
    }

    /// 迭代版欧几里得算法，避免递归栈开销
    fn gcd(mut a: i32, mut b: i32) -> i32 {
        while b != 0 {
            (a, b) = (b, a % b);
        }
        a
    }
}
```
