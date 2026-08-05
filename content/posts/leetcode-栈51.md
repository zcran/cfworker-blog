---
title: "leetcode-栈51"
date: 2026-07-24T10:17:00+08:00
tags: ["leetcode", "栈"]
draft: false
---


## 删除子串后的字符串最小长度

给你一个仅由 大写 英文字符组成的字符串 s 。

你可以对此字符串执行一些操作，在每一步操作中，你可以从 s 中删除 任一个 "AB" 或 "CD" 子字符串。

通过执行操作，删除所有 "AB" 和 "CD" 子串，返回可获得的最终字符串的 最小 可能长度。

注意，删除子串后，重新连接出的字符串可能会产生新的 "AB" 或 "CD" 子串。


```
impl Solution {
    /// 返回删除所有 "AB" 和 "CD" 子串后字符串的最小可能长度
    ///
    /// # 算法思路
    /// 使用栈模拟删除过程：
    /// - 遍历每个字符，将其压入栈
    /// - 如果栈顶与当前字符组成 "AB" 或 "CD"，则弹出栈顶（删除这对字符）
    /// - 否则将当前字符压入栈
    ///
    /// # 复杂度
    /// - 时间复杂度：O(n)，一次遍历
    /// - 空间复杂度：O(n)，栈空间
    pub fn min_length(s: String) -> i32 {
        let mut stack = Vec::with_capacity(s.len());

        for ch in s.chars() {
            // 检查当前字符是否与栈顶组成可删除的对
            match stack.last() {
                Some('A') if ch == 'B' => {
                    stack.pop(); // 删除 "AB"
                }
                Some('C') if ch == 'D' => {
                    stack.pop(); // 删除 "CD"
                }
                _ => {
                    stack.push(ch); // 无法配对，入栈
                }
            }
        }

        stack.len() as i32
    }
}
```
