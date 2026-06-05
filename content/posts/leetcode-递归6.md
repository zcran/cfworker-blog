---
title: "leetcode-递归6"
date: 2026-05-26T10:00:17+08:00
tags: ["leetcode", "递归"]
draft: false
---


## Pow(x, n)


实现 pow(x, n) ，即计算 x 的整数 n 次幂函数（即，x^n ）。

```
impl Solution {
    /// 快速幂（极致性能版）
    ///
    /// 优化点：
    /// 1. 使用 while 循环避免递归
    /// 2. 提前处理边界条件
    /// 3. 减少分支预测失败
    #[inline(never)]
    pub fn my_pow(mut x: f64, n: i32) -> f64 {
        // 边界快速返回
        match (x, n) {
            (_, 0) => return 1.0,
            (0.0, _) => return 0.0,
            (1.0, _) => return 1.0,
            (-1.0, _) => return if n & 1 == 0 { 1.0 } else { -1.0 },
            _ => {}
        }

        let mut exp = n as i64;
        let mut base = x;
        let mut result = 1.0;

        // 负指数优化
        if exp < 0 {
            base = 1.0 / base;
            exp = -exp;
        }

        // 主循环（展开以提升性能）
        while exp > 0 {
            if exp & 1 == 1 {
                result *= base;
            }
            base *= base;
            exp >>= 1;
        }

        result
    }
}
```
