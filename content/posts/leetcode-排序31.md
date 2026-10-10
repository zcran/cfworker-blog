---
title: "leetcode-排序31"
date: 2026-10-05T00:35:01+08:00
tags: ["leetcode", "排序"]
draft: false
---


## 通过翻转子数组使两个数组相等

给你两个长度相同的整数数组 target 和 arr 。每一步中，你可以选择 arr 的任意 非空子数组 并将它翻转。你可以执行此过程任意次。

如果你能让 arr 变得与 target 相同，返回 True；否则，返回 False 。


```
impl Solution {
    /// 判断 arr 能否通过任意次「翻转任意子数组」变为 target
    ///
    /// 关键观察：翻转子数组可生成任意排列（相邻交换可由长度 2 的翻转实现），
    /// 因此 arr 能变成 target 当且仅当两者元素多重集相同。
    /// 排序后逐一比较即可，不必真正模拟翻转。
    /// 时间 O(n log n)，额外空间 O(1)（原地排序）。
    pub fn can_be_equal(mut target: Vec<i32>, mut arr: Vec<i32>) -> bool {
        target.sort_unstable();
        arr.sort_unstable();
        target == arr
    }
}
```
