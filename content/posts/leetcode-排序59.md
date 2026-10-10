---
title: "leetcode-排序59"
date: 2026-10-05T00:35:03+08:00
tags: ["leetcode", "排序"]
draft: false
---


## 检查数组是否是好的

给你一个整数数组 nums ，如果它是数组 base[n] 的一个排列，我们称它是个 好 数组。

base[n] = [1, 2, ..., n - 1, n, n] （换句话说，它是一个长度为 n + 1 且包含 1 到 n - 1 恰好各一次，包含 n  两次的一个数组）。比方说，base[1] = [1, 1] ，base[3] = [1, 2, 3, 3] 。

如果数组是一个好数组，请你返回 true ，否则返回 false 。

注意：数组的排列是这些数字按任意顺序排布后重新得到的数组。


```
impl Solution {
    /// 判断 nums 是否为好数组。
    ///
    /// 好数组定义：长度为 n+1，包含 1..=n-1 各一次、n 恰好两次。
    /// 等价于：排序后前 n 个元素为 1..=n，且最后一个元素也为 n。
    /// 这里 n = nums.len() - 1。
    ///
    /// 思路：
    /// 1. 排序后依次检查前 n 个元素是否等于 1..=n。
    /// 2. 最后再检查末尾元素是否等于 n。
    pub fn is_good(mut nums: Vec<i32>) -> bool {
        nums.sort_unstable();
        let n = nums.len() - 1;

        // 前 n 个元素应依次为 1, 2, ..., n
        if nums[..n].iter().enumerate().any(|(i, &v)| v != (i + 1) as i32) {
            return false;
        }

        // 最后一个元素应为 n
        nums[n] == n as i32
    }
}
```
