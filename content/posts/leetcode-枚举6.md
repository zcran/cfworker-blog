---
title: "leetcode-枚举6"
date: 2026-07-09T10:04:59+08:00
tags: ["leetcode", "枚举"]
draft: false
---


## 强整数


给定三个整数 x 、 y 和 bound ，返回 值小于或等于 bound 的所有 强整数 组成的列表 。

如果某一整数可以表示为 xi + yj ，其中整数 i >= 0 且 j >= 0，那么我们认为该整数是一个 强整数 。

你可以按 任何顺序 返回答案。在你的回答中，每个值 最多 出现一次。

```
impl Solution {
    /// 返回所有不超过 bound 的强整数（x^i + y^j），结果不重复，顺序任意。
    pub fn powerful_integers(x: i32, y: i32, bound: i32) -> Vec<i32> {
        use std::collections::HashSet;

        // 特殊处理 x=1 和 y=1 的情况，避免无限循环
        let x_values = Self::powers(x, bound);
        let y_values = Self::powers(y, bound);

        let mut result = HashSet::new();

        for &xi in &x_values {
            for &yj in &y_values {
                let sum = xi + yj;
                if sum <= bound {
                    result.insert(sum);
                } else {
                    // 由于 y_values 递增，如果当前 yj 已经太大，后续 yj 更大，可以提前跳出
                    break;
                }
            }
        }

        result.into_iter().collect()
    }

    /// 生成所有不超过 bound 的 power 值（power^0, power^1, ...），
    /// 特殊处理 power=1 的情况，此时只生成 [1]。
    #[inline]
    fn powers(base: i32, bound: i32) -> Vec<i32> {
        let mut values = Vec::new();
        let mut current = 1;

        while current <= bound {
            values.push(current);
            if base == 1 {
                // 1 的任意次幂都是 1，避免死循环
                break;
            }
            // 检查是否溢出，防止无限增长
            if let Some(next) = current.checked_mul(base) {
                current = next;
            } else {
                break;
            }
        }

        values
    }
}
```
