---
title: "leetcode-数学16"
date: 2026-10-07T20:39:29+08:00
tags: ["leetcode", "数学"]
draft: false
---


## 绝对值表达式的最大值

给你两个长度相等的整数数组，返回下面表达式的最大值：

|arr1[i] - arr1[j]| + |arr2[i] - arr2[j]| + |i - j|

其中下标 i，j 满足 0 <= i, j < arr1.length。


```
impl Solution {
    /// 计算 max(|arr1[i]-arr1[j]| + |arr2[i]-arr2[j]| + |i-j|)。
    ///
    /// 利用绝对值展开技巧：对四个符号组合
    ///   ±arr1 ± arr2 ± i
    /// 分别求最大值与最小值之差，取四者中的最大值即为答案。
    ///
    /// 因为 |a| + |b| + |c| = max over 符号组合 of (±a ± b ± c)，
    /// 所以原式可化为四种线性表达式的极差。
    pub fn max_abs_val_expr(arr1: Vec<i32>, arr2: Vec<i32>) -> i32 {
        // 分别维护四个表达式的最小值与最大值
        let mut max = [i32::MIN; 4];
        let mut min = [i32::MAX; 4];

        for (i, (&a, &b)) in arr1.iter().zip(arr2.iter()).enumerate() {
            let i = i as i32;
            // 四种符号组合
            let vals = [a + b + i, a + b - i, a - b + i, a - b - i];

            for (k, &v) in vals.iter().enumerate() {
                if v > max[k] { max[k] = v; }
                if v < min[k] { min[k] = v; }
            }
        }

        // 四种组合极差的最大值即为答案
        (0..4).map(|k| max[k] - min[k]).max().unwrap()
    }
}
```
