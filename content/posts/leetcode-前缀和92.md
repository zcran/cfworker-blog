---
title: "leetcode-前缀和92"
date: 2026-08-25T10:02:51+08:00
tags: ["leetcode", "前缀和"]
draft: false
---


## 找到最大周长的多边形

给你一个长度为 n 的 正 整数数组 nums 。

多边形 指的是一个至少有 3 条边的封闭二维图形。多边形的 最长边 一定 小于 所有其他边长度之和。

如果你有 k （k >= 3）个 正 数 a1，a2，a3, ...，ak 满足 a1 <= a2 <= a3 <= ... <= ak 且 a1 + a2 + a3 + ... + ak-1 > ak ，那么 一定 存在一个 k 条边的多边形，每条边的长度分别为 a1 ，a2 ，a3 ， ...，ak 。

一个多边形的 周长 指的是它所有边之和。

请你返回从 nums 中可以构造的 多边形 的 最大周长 。如果不能构造出任何多边形，请你返回 -1 。


```
impl Solution {
    pub fn largest_perimeter(mut nums: Vec<i32>) -> i64 {
        // 按升序排序，便于贪心选择
        nums.sort_unstable();
        let n = nums.len();

        // 计算所有元素的和
        let mut sum: i64 = nums.iter().map(|&x| x as i64).sum();

        // 从最大边开始尝试移除，直到满足多边形条件
        for i in (2..n).rev() {
            let longest = nums[i] as i64;
            let others = sum - longest;
            // 多边形条件：最长边 < 其他边之和
            if longest < others {
                return sum; // 当前所有边能构成多边形
            }
            // 否则移除当前最长边，继续检查
            sum -= longest;
        }

        -1
    }
}
```
