---
title: "leetcode-栈47"
date: 2026-07-24T10:17:00+08:00
tags: ["leetcode", "栈"]
draft: false
---


## 从字符串中移除星号

给你一个包含若干星号 * 的字符串 s 。

在一步操作中，你可以：

选中 s 中的一个星号。

移除星号 左侧 最近的那个 非星号 字符，并移除该星号自身。

返回移除 所有 星号之后的字符串。

注意：

生成的输入保证总是可以执行题面中描述的操作。

可以证明结果字符串是唯一的。


```
impl Solution {
    /// 移除字符串中所有星号及其左侧最近的非星号字符
    ///
    /// # 算法思路
    /// 使用栈模拟删除操作：
    /// - 遇到非星号字符：入栈
    /// - 遇到星号：弹出栈顶的非星号字符（即删除左侧最近的有效字符）
    pub fn remove_stars(s: String) -> String {
        let mut stack = Vec::with_capacity(s.len());

        for ch in s.chars() {
            if ch == '*' {
                // 星号：删除左侧最近的非星号字符
                stack.pop();
            } else {
                // 普通字符：入栈保留
                stack.push(ch);
            }
        }

        // 将栈中剩余字符转换为字符串
        stack.into_iter().collect()
    }
}
```
