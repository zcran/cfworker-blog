---
title: "Leetcode 快速选择6"
date: 2023-12-19T15:49:31+08:00
tags: ["leetcode", "快速选择"]
draft: false
---

## 最小K个数

设计一个算法，找出数组中最小的k个数。以任意顺序返回这k个数均可。

示例：

输入： arr = [1,3,5,7,2,4,6,8], k = 4
输出： [1,2,3,4]
提示：

0 <= len(arr) <= 100000
0 <= k <= min(100000, len(arr))

```
impl Solution {
  pub fn get_least_numbers(arr: Vec<i32>, k: i32) -> Vec<i32> {
    let mut arr = arr;
    arr.sort();
    arr[..k as usize].iter().cloned().collect()
  }
}
```