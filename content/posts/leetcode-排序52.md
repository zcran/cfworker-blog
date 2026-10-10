---
title: "leetcode-排序52"
date: 2026-10-05T00:35:03+08:00
tags: ["leetcode", "排序"]
draft: false
---


## 按身高排序

给你一个字符串数组 names ，和一个由 互不相同 的正整数组成的数组 heights 。两个数组的长度均为 n 。

对于每个下标 i，names[i] 和 heights[i] 表示第 i 个人的名字和身高。

请按身高 降序 顺序返回对应的名字数组 names 。


```
impl Solution {
    /// 按身高降序返回对应的名字数组。
    ///
    /// 思路：
    /// 将 (身高, 名字) 配对后按身高降序排序，再取出名字。
    pub fn sort_people(names: Vec<String>, heights: Vec<i32>) -> Vec<String> {
        let mut pairs: Vec<(i32, String)> = heights.into_iter().zip(names).collect();
        pairs.sort_unstable_by(|a, b| b.0.cmp(&a.0));
        pairs.into_iter().map(|(_, name)| name).collect()
    }
}
```
