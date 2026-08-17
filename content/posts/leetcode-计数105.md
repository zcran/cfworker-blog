---
title: "leetcode-计数105"
date: 2026-08-01T10:40:56+08:00
tags: ["leetcode", "计数"]
draft: false
---


##  1 移动到末尾的最大操作次数

给你一个 二进制字符串 s。

你可以对这个字符串执行 任意次 下述操作：

选择字符串中的任一下标 i（ i + 1 < s.length ），该下标满足 s[i] == '1' 且 s[i + 1] == '0'。

将字符 s[i] 向 右移 直到它到达字符串的末端或另一个 '1'。例如，对于 s = "010010"，如果我们选择 i = 1，结果字符串将会是 s = "000110"。

返回你能执行的 最大 操作次数。


```
impl Solution {
    pub fn max_operations(s: String) -> i32 {
        let mut ones = 0;
        let mut ans = 0;
        let mut in_zero_block = false;

        for b in s.bytes() {
            if b == b'1' {
                ones += 1;
                in_zero_block = false;
            } else if !in_zero_block {
                // 遇到 '0' 块的第一个 '0'
                ans += ones;
                in_zero_block = true;
            }
            // 块内其他 '0' 不做处理
        }

        ans
    }
}
```
