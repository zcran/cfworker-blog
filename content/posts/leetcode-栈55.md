---
title: "leetcode-栈55"
date: 2026-07-24T10:17:00+08:00
tags: ["leetcode", "栈"]
draft: false
---


## 清除数字

给你一个字符串 s 。

你的任务是重复以下操作删除 所有 数字字符：

删除 第一个数字字符 以及它左边 最近 的 非数字 字符。

请你返回删除所有数字字符以后剩下的字符串。

注意，该操作不能对左侧没有任何非数字字符的数字执行。


```
impl Solution {
    /// 删除所有数字字符及其左侧最近的非数字字符，返回剩余字符串
    ///
    /// # 算法思路
    /// 使用栈模拟删除操作：
    /// - 遇到非数字字符：入栈
    /// - 遇到数字字符：弹出栈顶的非数字字符（删除该数字及左侧最近字符）
    ///
    /// # 复杂度
    /// - 时间复杂度：O(n)，一次遍历
    /// - 空间复杂度：O(n)，栈空间
    pub fn clear_digits(s: String) -> String {
        let mut stack = Vec::with_capacity(s.len());

        for ch in s.chars() {
            if ch.is_ascii_digit() {
                // 数字字符：删除左侧最近的非数字字符（弹出栈顶）
                stack.pop();
            } else {
                // 非数字字符：入栈保留
                stack.push(ch);
            }
        }

        // 将栈中剩余字符转换为字符串
        stack.into_iter().collect()
    }
}
```
