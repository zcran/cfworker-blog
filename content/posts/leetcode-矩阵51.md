---
title: "leetcode-矩阵51"
date: 2026-09-19T10:41:35+08:00
tags: ["leetcode", "矩阵"]
draft: false
---


## 根据第 K 场考试的分数排序

班里有 m 位学生，共计划组织 n 场考试。给你一个下标从 0 开始、大小为 m x n 的整数矩阵 score ，其中每一行对应一位学生，而 score[i][j] 表示第 i 位学生在第 j 场考试取得的分数。矩阵 score 包含的整数 互不相同 。

另给你一个整数 k 。请你按第 k 场考试分数从高到低完成对这些学生（矩阵中的行）的排序。

返回排序后的矩阵。


```
impl Solution {
    /// 按第 k 场考试分数从高到低排序学生（矩阵行）。
    /// 各场分数互不相同，无需考虑并列；`sort_by` 为稳定排序但此处无所谓。
    /// 直接对入参 `score` 排序并返回，省去一次 clone。
    pub fn sort_the_students(mut score: Vec<Vec<i32>>, k: i32) -> Vec<Vec<i32>> {
        let k = k as usize;
        score.sort_unstable_by(|a, b| b[k].cmp(&a[k]));
        score
    }
}
```
