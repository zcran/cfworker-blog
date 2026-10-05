---
title: "leetcode-贪心74"
date: 2026-10-03T12:55:39+08:00
tags: ["leetcode", "贪心"]
draft: false
---


## 减小和重新排列数组后的最大元素

给你一个正整数数组 arr 。请你对 arr 执行一些操作（也可以不进行任何操作），使得数组满足以下条件：

· arr 中 第一个 元素必须为 1 。
· 任意相邻两个元素的差的绝对值 小于等于 1 ，也就是说，对于任意的 1 <= i < arr.length （数组下标从 0 开始），都满足 abs(arr[i] - arr[i - 1]) <= 1 。abs(x) 为 x 的绝对值。

你可以执行以下 2 种操作任意次：

· 减小 arr 中任意元素的值，使其变为一个 更小的正整数 。
· 重新排列 arr 中的元素，你可以以任意顺序重新排列。

请你返回执行以上操作后，在满足前文所述的条件下，arr 中可能的 最大值 。


```
impl Solution {
    pub fn maximum_element_after_decrementing_and_rearranging(mut arr: Vec<i32>) -> i32 {
        // 排序后贪心：第一个固定为 1，后续每个元素最多比前一个大 1
        arr.sort_unstable();

        let mut prev = 1;
        for &x in &arr[1..] {
            prev = x.min(prev + 1);
        }
        prev
    }
}
```
