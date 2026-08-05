---
title: "leetcode-计数42"
date: 2026-08-01T10:40:53+08:00
tags: ["leetcode", "计数"]
draft: false
---


## 仅执行一次字符串交换能否使两个字符串相等

给你长度相等的两个字符串 s1 和 s2 。一次 字符串交换 操作的步骤如下：选出某个字符串中的两个下标（不必不同），并交换这两个下标所对应的字符。

如果对 其中一个字符串 执行 最多一次字符串交换 就可以使两个字符串相等，返回 true ；否则，返回 false 。


```
impl Solution {
    pub fn are_almost_equal(s1: String, s2: String) -> bool {
        let (s1, s2) = (s1.as_bytes(), s2.as_bytes());

        // 收集所有不匹配的位置
        let mut diff = Vec::with_capacity(2);
        for i in 0..s1.len() {
            if s1[i] != s2[i] {
                diff.push(i);
                if diff.len() > 2 {
                    return false;
                }
            }
        }

        match diff.len() {
            0 => true,                                   // 完全相同
            2 => {                                        // 恰好两处不同，检查能否交换
                let (i, j) = (diff[0], diff[1]);
                s1[i] == s2[j] && s1[j] == s2[i]
            }
            _ => false,                                   // 1处或3处以上不同
        }
    }
}
```
