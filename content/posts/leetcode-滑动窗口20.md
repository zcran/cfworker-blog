---
title: "leetcode-滑动窗口20"
date: 2026-07-18T11:02:30+08:00
tags: ["leetcode", "滑动窗口"]
draft: false
---


## 最长湍流子数组

给定一个整数数组 arr ，返回 arr 的 最大湍流子数组的长度 。

如果比较符号在子数组中的每个相邻元素对之间翻转，则该子数组是 湍流子数组 。

更正式地来说，当 arr 的子数组 A[i], A[i+1], ..., A[j] 满足仅满足下列条件时，我们称其为湍流子数组：

· 若 i <= k < j ：
    · 当 k 为奇数时， A[k] > A[k+1]，且
    · 当 k 为偶数时，A[k] < A[k+1]；
· 或 若 i <= k < j ：
    · 当 k 为偶数时，A[k] > A[k+1] ，且
    · 当 k 为奇数时， A[k] < A[k+1]。


```
impl Solution {
    pub fn max_turbulence_size(arr: Vec<i32>) -> i32 {
        let n = arr.len();
        if n < 2 {
            return n as i32;
        }

        let mut max_len = 1;
        let mut inc = 1; // 以当前元素结尾且最后一段是上升的子数组长度
        let mut dec = 1; // 以当前元素结尾且最后一段是下降的子数组长度

        for i in 1..n {
            if arr[i - 1] < arr[i] {
                // 上升：前一段必须是下降
                inc = dec + 1;
                dec = 1;
                max_len = max_len.max(inc);
            } else if arr[i - 1] > arr[i] {
                // 下降：前一段必须是上升
                dec = inc + 1;
                inc = 1;
                max_len = max_len.max(dec);
            } else {
                // 相等，重置
                inc = 1;
                dec = 1;
            }
        }

        max_len
    }
}
```
