---
title: "leetcode-单调栈50"
date: 2026-06-07T10:14:06+08:00
tags: ["leetcode", "单调栈"]
draft: false
---


## 删除重复字符后的字典序最小字符串

给你一个字符串 s，它由小写英文字母组成。

你可以进行如下操作任意次（可能为零次）：

选择当前字符串 s 中 至少出现两次 的任意一个字母并删除其中的一次出现。
返回可以通过这种方式形成的 字典序最小 的结果字符串。

如果字符串 a 的某个位置与字符串 b 不同，且 a 在该位置的字母比 b 对应位置的字母在字母表中更靠前，则 a 被认为是 字典序更小 的字符串。如果 a 的前 min(a.length, b.length) 个字符都与 b 相同，则较短的字符串字典序更小。

```
impl Solution {
    /// 返回通过删除至少出现两次的字母（每次删一个）能得到的最小字典序字符串
    ///
    /// 核心思路：贪心 + 单调栈
    /// 1. 从左到右处理字符，维护一个单调递增栈（字典序递增）
    /// 2. 如果当前字符比栈顶小，且栈顶字符后面还有剩余（可删），则弹出栈顶
    /// 3. 最后从尾部删除多余的重复字符（因为保留一个即可）
    pub fn lex_smallest_after_deletion(s: String) -> String {
        let mut cnt = [0; 26];
        for &b in s.as_bytes() {
            cnt[(b - b'a') as usize] += 1;
        }

        let mut stack = Vec::with_capacity(s.len());
        for &b in s.as_bytes() {
            let idx = (b - b'a') as usize;
            // 当前字符比栈顶小，且栈顶字符后面还有（可删），则弹出
            while let Some(&top) = stack.last() {
                if b < top && cnt[(top - b'a') as usize] > 1 {
                    cnt[(top - b'a') as usize] -= 1;
                    stack.pop();
                } else {
                    break;
                }
            }
            stack.push(b);
            // 注意：cnt 在这里不减，因为只是入栈，还没真正删除
        }

        // 从尾部移除多余的重复字符（每个字母只保留一次）
        while let Some(&last) = stack.last() {
            if cnt[(last - b'a') as usize] > 1 {
                cnt[(last - b'a') as usize] -= 1;
                stack.pop();
            } else {
                break;
            }
        }

        String::from_utf8(stack).unwrap()
    }
}
```
