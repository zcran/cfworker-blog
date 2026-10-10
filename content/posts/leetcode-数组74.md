---
title: "leetcode-数组74"
date: 2026-10-10T20:38:45+08:00
tags: ["leetcode", "数组"]
draft: false
---


## 成对操作转化数组

给你两个整数数组 source 和 target。

在一次 操作 中，你可以选择 source 中两个 不同 的下标 i 和 j，以及任何整数 delta。
然后按如下方式更新 source：

source[i] = source[i] + source[j] - delta
source[j] = delta
如果在执行该操作 任意 次（包括零次）后能够使 source 等于 target，则返回 true。否则，返回 false。


```
impl Solution {
    pub fn can_transform(source: Vec<i32>, target: Vec<i32>) -> bool {
        // 操作保持数组元素总和不变，因此仅需比较总和
        let sum_s: i64 = source.iter().map(|&x| x as i64).sum();
        let sum_t: i64 = target.iter().map(|&x| x as i64).sum();
        sum_s == sum_t
    }
}
```
