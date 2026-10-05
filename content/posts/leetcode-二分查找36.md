---
title: "leetcode-二分查找36"
date: 2026-09-28T12:29:12+08:00
tags: ["leetcode", "二分查找"]
draft: false
---


## 转变数组后最接近目标值的数组和

给你一个整数数组 arr 和一个目标值 target ，请你返回一个整数 value ，使得将数组中所有大于 value 的值变成 value 后，数组的和最接近  target （最接近表示两者之差的绝对值最小）。

如果有多种使得和最接近 target 的方案，请你返回这些整数中的最小值。

请注意，答案不一定是 arr 中的数字。


```
impl Solution {
    /// 返回使「将 arr 中所有大于 value 的元素截断为 value 后的数组和」最接近 target 的最小 value。
    ///
    /// 思路：二分 + 前后比较。
    ///   设 f(v) = sum(min(x, v))，它是关于 v 的单调非减函数。
    ///   二分找到最小的 v 使 f(v) >= target（记为 lo），
    ///   则答案必在 lo 与 lo-1 中（f(lo-1) < target <= f(lo)），
    ///   比较两者与 target 的差，取差小者；若相等取较小值。
    pub fn find_best_value(arr: Vec<i32>, target: i32) -> i32 {
        // 计算截断值为 v 时的数组和
        let sum = |v: i32| -> i32 { arr.iter().map(|&x| x.min(v)).sum() };

        // 二分找最小 v 使 f(v) >= target
        let mut lo = 0;
        let mut hi = *arr.iter().max().unwrap();
        while lo < hi {
            let mid = lo + (hi - lo) / 2;
            if sum(mid) >= target {
                hi = mid;
            } else {
                lo = mid + 1;
            }
        }

        // 比较 lo 与 lo-1 哪个更接近 target，相等时取较小值
        if lo > 0 && (sum(lo - 1) - target).abs() <= (sum(lo) - target).abs() {
            lo - 1
        } else {
            lo
        }
    }
}
```
