---
title: "leetcode-位运算5"
date: 2026-09-24T10:33:34+08:00
tags: ["leetcode", "位运算"]
draft: false
---


## 子数组按位或操作

给定一个整数数组 arr，返回所有 arr 的非空子数组的不同按位或的数量。

子数组的按位或是子数组中每个整数的按位或。含有一个整数的子数组的按位或就是该整数。

子数组 是数组内连续的非空元素序列。


```
use std::collections::HashSet;

impl Solution {
    /// 核心思路：
    /// 以 arr[i] 结尾的所有子数组的按位或，可以由「以 arr[i-1] 结尾的所有按位或」再或上 arr[i] 得到。
    /// 由于按位或只会让 1 的个数单调不减，不同结果的数量非常有限（最多约 32 个），
    /// 因此可以用一个集合维护当前结尾的所有可能或值，并全局记录出现过的所有结果。
    ///
    /// 实现上，直接原地复用 arr 的前缀位置作为「以当前元素结尾的所有或值」的存储，
    /// 并利用 or 的单调性提前 break 去重。
    pub fn subarray_bitwise_o_rs(mut arr: Vec<i32>) -> i32 {
        let mut seen = HashSet::new();

        for i in 0..arr.len() {
            seen.insert(arr[i]);

            // 从 i-1 向前遍历，逐步把 arr[i] 或进前面的累积值
            for j in (0..i).rev() {
                // 如果 arr[j] 已经包含了 arr[i] 的所有 1 位，则更早的也一定包含，直接退出
                if arr[j] | arr[i] == arr[j] {
                    break;
                }
                arr[j] |= arr[i];
                seen.insert(arr[j]);
            }
        }

        seen.len() as i32
    }
}
```
