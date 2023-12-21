---
title: "Leetcode 计数排序4"
date: 2023-12-19T15:45:23+08:00
tags: ["leetcode", "计数排序"]
draft: false
---

## H 指数

给你一个整数数组 citations ，其中 citations[i] 表示研究者的第 i 篇论文被引用的次数。计算并返回该研究者的 h 指数。

根据维基百科上 h 指数的定义：h 代表“高引用次数” ，一名科研人员的 h 指数 是指他（她）至少发表了 h 篇论文，并且 至少 有 h 篇论文被引用次数大于等于 h 。如果 h 有多种可能的值，h 指数 是其中最大的那个。

```
impl Solution {
    pub fn h_index(citations: Vec<i32>) -> i32 {
        let mut left_index = 0;
        let mut right_index = citations.len();

        let mut ans = 0;

        while left_index <= right_index {
            let mid = left_index + (right_index - left_index) / 2;

            let mut tmp_count = 0;
            for &item in citations.iter() {
                if item >= mid as i32 {
                    tmp_count += 1;
                }
            }

            if tmp_count >= mid {
                left_index = mid + 1;
                ans = mid;
            } else {
                right_index = mid - 1;
            }
        }

        ans as i32
    }
}
```