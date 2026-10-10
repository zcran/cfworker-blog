---
title: "leetcode-排序21"
date: 2026-10-05T00:35:00+08:00
tags: ["leetcode", "排序"]
draft: false
---


## 高度检查器

学校打算为全体学生拍一张年度纪念照。根据要求，学生需要按照 非递减 的高度顺序排成一行。

排序后的高度情况用整数数组 expected 表示，其中 expected[i] 是预计排在这一行中第 i 位的学生的高度（下标从 0 开始）。

给你一个整数数组 heights ，表示 当前学生站位 的高度情况。heights[i] 是这一行中第 i 位学生的高度（下标从 0 开始）。

返回满足 heights[i] != expected[i] 的 下标数量 。


```
impl Solution {
    /// 返回 heights 与「非递减排序后」对应位置不同的下标数量
    ///
    /// 思路：高度范围固定为 [1, 100]，用计数排序求每个位置应有的高度。
    /// 先统计频次并做前缀和，得到每个高度在排序后数组中的结束位置；
    /// 再从后往前遍历 heights，把每个高度放到其正确位置并比对。
    /// 时间 O(n + 100)，额外空间 O(100)。
    pub fn height_checker(heights: Vec<i32>) -> i32 {
        // cnt[h] 统计高度 h 出现的次数（下标 0 空闲不用）
        let mut cnt = [0i32; 101];
        for &h in &heights {
            cnt[h as usize] += 1;
        }

        // 前缀和：cnt[h] 变为高度 ≤ h 的元素个数（即高度 h 的结束位置）
        for h in 1..101 {
            cnt[h] += cnt[h - 1];
        }

        let mut mismatches = 0;
        // 从后往前放置，cnt[h]-1 即为高度 h 下一个应放入的位置
        for &h in heights.iter().rev() {
            cnt[h as usize] -= 1;
            // 排序后该位置的高度，应当等于 h 本身；不等说明原位错位
            if heights[cnt[h as usize] as usize] != h {
                mismatches += 1;
            }
        }

        mismatches
    }
}
```
