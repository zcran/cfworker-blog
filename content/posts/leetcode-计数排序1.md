---
title: "Leetcode 计数排序1"
date: 2023-12-19T15:45:23+08:00
tags: ["leetcode", "计数排序"]
draft: false
---

## 高度检查器

学校打算为全体学生拍一张年度纪念照。根据要求，学生需要按照 非递减 的高度顺序排成一行。

排序后的高度情况用整数数组 expected 表示，其中 expected[i] 是预计排在这一行中第 i 位的学生的高度（下标从 0 开始）。

给你一个整数数组 heights ，表示 当前学生站位 的高度情况。heights[i] 是这一行中第 i 位学生的高度（下标从 0 开始）。

返回满足 heights[i] != expected[i] 的 下标数量 。


```
impl Solution {
    pub fn height_checker(heights: Vec<i32>) -> i32 {
        let mut cnt = vec![0; 101];
        heights.iter().for_each(|i| cnt[*i as usize] += 1);
        for i in 1..101 { cnt[i] += cnt[i - 1]; }
        let mut ret = 0;
        for i in 0..heights.len() {
            cnt[heights[i] as usize] -= 1;
            if heights[i] != heights[cnt[heights[i] as usize] as usize] { ret += 1; }
        }
        ret
    }
}
```