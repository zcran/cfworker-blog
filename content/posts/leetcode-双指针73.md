---
title: "leetcode-双指针73"
date: 2026-09-05T09:53:05+08:00
tags: ["leetcode", "双指针"]
draft: false
---


##  删除字符串两端相同字符后的最短长度

给你一个只包含字符 'a'，'b' 和 'c' 的字符串 s ，你可以执行下面这个操作（5 个步骤）任意次：

1. 选择字符串 s 一个 非空 的前缀，这个前缀的所有字符都相同。
2. 选择字符串 s 一个 非空 的后缀，这个后缀的所有字符都相同。
3. 前缀和后缀在字符串中任意位置都不能有交集。
4. 前缀和后缀包含的所有字符都要相同。
5. 同时删除前缀和后缀。

请你返回对字符串 s 执行上面操作任意次以后（可能 0 次），能得到的 最短长度 。


```
impl Solution {
    pub fn minimum_length(s: String) -> i32 {
        let bytes = s.as_bytes();
        let (mut left, mut right) = (0, bytes.len() - 1);

        while left < right && bytes[left] == bytes[right] {
            let ch = bytes[left];

            // 删除左侧所有相同字符
            while left <= right && bytes[left] == ch {
                left += 1;
            }

            // 删除右侧所有相同字符
            while left <= right && bytes[right] == ch {
                right -= 1;
            }
        }

        // 剩余字符数 = right - left + 1
        // 如果 left > right，说明字符串为空
        if left > right { 0 } else { (right - left + 1) as i32 }
    }
}
```
