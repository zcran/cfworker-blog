---
title: "leetcode-单调栈25"
date: 2026-06-07T10:14:04+08:00
tags: ["leetcode", "单调栈"]
draft: false
---


## 形成目标数组的子数组最少增加次数

给你一个整数数组 target 和一个数组 initial ，initial 数组与 target  数组有同样的大小，且一开始全部为 0 。

一次操作中，你可以从 initial 数组中选择 任何 子数组，并将每个值加 1。

返回从 initial 数组构造 target 数组的最少操作次数。

答案保证在 32 位整数以内。

```
impl Solution {
    pub fn min_number_operations(target: Vec<i32>) -> i32 {
        let n = target.len();
        let mut ans = target[0];
        for i in 1..n {
            ans += std::cmp::max(target[i] - target[i - 1], 0);
        }
        ans
    }
}
```
