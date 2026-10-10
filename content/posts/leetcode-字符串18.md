---
title: "leetcode-字符串18"
date: 2026-10-10T12:56:52+08:00
tags: ["leetcode", "字符串"]
draft: false
---


## 删列造序

给你由 n 个小写字母字符串组成的数组 strs，其中每个字符串长度相等。

这些字符串可以每个一行，排成一个网格。例如，strs = ["abc", "bce", "cae"] 可以排列为：

abc
bce
cae

你需要找出并删除 不是按字典序非严格递增排列的 列。在上面的例子（下标从 0 开始）中，列 0（'a', 'b', 'c'）和列 2（'c', 'e', 'e'）都是按字典序非严格递增排列的，而列 1（'b', 'c', 'a'）不是，所以要删除列 1 。

返回你需要删除的列数。


```
impl Solution {
    pub fn min_deletion_size(strs: Vec<String>) -> i32 {
        // 逐列检查：只要某列出现「上 > 下」即违反非严格递增，计入删除
        let rows: Vec<&[u8]> = strs.iter().map(|s| s.as_bytes()).collect();
        let m = rows[0].len();

        (0..m)
            .filter(|&j| rows.windows(2).any(|w| w[0][j] > w[1][j]))
            .count() as i32
    }
}
```
