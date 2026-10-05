---
title: "leetcode-双指针123"
date: 2026-09-05T09:53:08+08:00
tags: ["leetcode", "双指针"]
draft: false
---


## 字典序最小和为目标值且绝对值是排列的数组

给你一个正整数 n 和一个整数 target。

请返回一个大小为 n 的 字典序最小 的整数数组，并满足：

其元素 和 等于 target。
其元素的 绝对值 组成一个大小为 n 的 排列。

如果不存在这样的数组，则返回一个空数组。

如果数组 a 和 b 在第一个不同的位置上，数组 a 的元素小于 b 的对应元素，则认为数组 a 字典序小于 数组 b。

大小为 n 的 排列 是对整数 1, 2, ..., n 的重新排列。


```
impl Solution {
    pub fn lex_smallest_negated_perm(n: i32, target: i64) -> Vec<i32> {
        let n = n as usize;
        // 1..n 的总和
        let total: i64 = (n as i64) * (n as i64 + 1) / 2;

        // 可行性检查：target 绝对值不能超过总和，且 total - target 必须为偶数
        if target.abs() > total || (total - target) % 2 != 0 {
            return vec![];
        }

        // 需要取负的数的和
        let mut need = (total - target) / 2;

        let mut negatives = Vec::new();
        let mut positives = Vec::new();

        // 从大到小贪心选择负数，使得负数部分绝对值尽可能大，
        // 这样负数在数组前面会更小，从而字典序最小。
        for i in (1..=n).rev() {
            let val = i as i64;
            if val <= need {
                negatives.push(-(i as i32));
                need -= val;
            } else {
                positives.push(i as i32);
            }
        }

        // positives 目前是从大到小，为了字典序最小，正数部分应从小到大排列。
        positives.reverse();

        // 负数部分已经是按绝对值从大到小（即最负的在最前），直接拼接正数部分。
        negatives.extend(positives);
        negatives
    }
}
```
