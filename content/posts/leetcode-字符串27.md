---
title: "leetcode-字符串27"
date: 2026-10-10T12:56:53+08:00
tags: ["leetcode", "字符串"]
draft: false
---


## 千位分隔数

给你一个整数 n，请你每隔三位添加点（即 "." 符号）作为千位分隔符，并将结果以字符串格式返回。


```
impl Solution {
    pub fn thousand_separator(n: i32) -> String {
        let s = n.to_string();
        let bytes = s.as_bytes();
        let len = bytes.len();
        // 预留：字符数 + 最多 (len-1)/3 个点
        let mut result = String::with_capacity(len + (len - 1) / 3);

        for (i, &b) in bytes.iter().enumerate() {
            // 从左边数，剩余字符数不是 3 的倍数时，下一个位置就插点
            // 等价于：当前位置距末尾的间隔为 3 的倍数且不是开头，则先补点
            if i > 0 && (len - i) % 3 == 0 {
                result.push('.');
            }
            result.push(b as char);
        }

        result
    }
}
```
