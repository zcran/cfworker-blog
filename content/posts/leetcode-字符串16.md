---
title: "leetcode-字符串16"
date: 2026-10-10T12:56:52+08:00
tags: ["leetcode", "字符串"]
draft: false
---


## 较大分组的位置

在一个由小写字母构成的字符串 s 中，包含由一些连续的相同字符所构成的分组。

例如，在字符串 s = "abbxxxxzyy" 中，就含有 "a", "bb", "xxxx", "z" 和 "yy" 这样的一些分组。

分组可以用区间 [start, end] 表示，其中 start 和 end 分别表示该分组的起始和终止位置的下标。上例中的 "xxxx" 分组用区间表示为 [3,6] 。

我们称所有包含大于或等于三个连续字符的分组为 较大分组 。

找到每一个 较大分组 的区间，按起始位置下标递增顺序排序后，返回结果。


```
impl Solution {
    pub fn large_group_positions(s: String) -> Vec<Vec<i32>> {
        let s = s.as_bytes();
        let n = s.len();
        let mut result = Vec::new();
        let mut start = 0; // 当前分组的起始下标

        // 遍历到 n 处（哨兵），保证最后一组也能被收尾处理
        for i in 0..=n {
            // 到达末尾或字符发生变化时，结算 [start, i-1] 这个分组
            if i == n || s[i] != s[start] {
                if i - start >= 3 {
                    result.push(vec![start as i32, (i - 1) as i32]);
                }
                start = i;
            }
        }

        result
    }
}
```
