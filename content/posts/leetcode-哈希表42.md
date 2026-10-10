---
title: "leetcode-哈希表42"
date: 2026-10-09T20:07:58+08:00
tags: ["leetcode", "哈希表"]
draft: false
---


## 能否连接形成数组

给你一个整数数组 arr ，数组中的每个整数 互不相同 。另有一个由整数数组构成的数组 pieces，其中的整数也 互不相同 。请你以 任意顺序 连接 pieces 中的数组以形成 arr 。但是，不允许 对每个数组 pieces[i] 中的整数重新排序。

如果可以连接 pieces 中的数组形成 arr ，返回 true ；否则，返回 false 。


```
use std::collections::HashMap;

impl Solution {
    pub fn can_form_array(arr: Vec<i32>, pieces: Vec<Vec<i32>>) -> bool {
        // 记录 arr 中每个值的下标。pieces 中每个片段在 arr 中必须连续，
        // 即片段内每个元素的下标依次递增 1，且首元素必须出现在 arr 中。
        let pos: HashMap<i32, usize> = arr.iter().enumerate().map(|(i, &v)| (v, i)).collect();

        pieces.iter().all(|piece| {
            // 片段首元素在 arr 中的位置决定后续元素的期望下标。
            match pos.get(&piece[0]) {
                None => false,
                Some(&start) => piece
                    .iter()
                    .enumerate()
                    .all(|(k, v)| pos.get(v) == Some(&(start + k))),
            }
        })
    }
}
```
