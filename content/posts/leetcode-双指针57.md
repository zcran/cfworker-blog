---
title: "leetcode-双指针57"
date: 2026-09-05T09:53:04+08:00
tags: ["leetcode", "双指针"]
draft: false
---


## 增减字符串匹配

由范围 [0,n] 内所有整数组成的 n + 1 个整数的排列序列可以表示为长度为 n 的字符串 s ，其中:

如果 perm[i] < perm[i + 1] ，那么 s[i] == 'I'
如果 perm[i] > perm[i + 1] ，那么 s[i] == 'D'

给定一个字符串 s ，重构排列 perm 并返回它。如果有多个有效排列perm，则返回其中 任何一个 。


```
impl Solution {
    pub fn di_string_match(s: String) -> Vec<i32> {
        let mut result = Vec::with_capacity(s.len() + 1);
        let mut low = 0;
        let mut high = s.len() as i32;

        // 遇到 'I' 放当前最小值，遇到 'D' 放当前最大值
        for ch in s.bytes() {
            if ch == b'I' {
                result.push(low);
                low += 1;
            } else {
                // ch == b'D'
                result.push(high);
                high -= 1;
            }
        }

        // 最后剩余一个数
        result.push(low);

        result
    }
}
```
