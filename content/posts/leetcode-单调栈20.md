---
title: "leetcode-单调栈20"
date: 2026-06-07T10:14:04+08:00
tags: ["leetcode", "单调栈"]
draft: false
---


## 不同字符的最小子序列

返回 s 字典序最小的子序列，该子序列包含 s 的所有不同字符，且只包含一次。

```
impl Solution {
    pub fn smallest_subsequence(s: String) -> String {
        let s = s.as_bytes();
        let mut last_occ = [0; 26];  // 记录每个字符最后出现的位置
        let mut in_stack = [false; 26]; // 记录字符是否已在栈中
        let mut stack = Vec::with_capacity(26); // 单调栈，存储字符

        // 统计每个字符最后出现的位置
        for (i, &ch) in s.iter().enumerate() {
            last_occ[(ch - b'a') as usize] = i;
        }

        for (i, &ch) in s.iter().enumerate() {
            let idx = (ch - b'a') as usize;

            if in_stack[idx] {
                continue; // 已存在栈中，跳过
            }

            // 单调栈：保持栈内字符递增，且后面的字符有机会出现
            while let Some(&top) = stack.last() {
                let top_idx = (top - b'a') as usize;
                if top > ch && last_occ[top_idx] > i {
                    // 栈顶字符更大且后面还会出现，弹出
                    in_stack[top_idx] = false;
                    stack.pop();
                } else {
                    break;
                }
            }

            // 当前字符入栈
            stack.push(ch);
            in_stack[idx] = true;
        }

        String::from_utf8(stack).unwrap()
    }
}
```
