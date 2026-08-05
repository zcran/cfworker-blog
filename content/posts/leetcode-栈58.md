---
title: "leetcode-栈58"
date: 2026-07-24T10:17:00+08:00
tags: ["leetcode", "栈"]
draft: false
---


## 移除K-平衡子字符串

给你一个只包含 '(' 和 ')' 的字符串 s，以及一个整数 k。

如果一个 字符串 恰好是 k 个 连续 的 '(' 后面跟着 k 个 连续 的 ')'，即 '(' * k + ')' * k ，那么称它是 k-平衡 的。

例如，如果 k = 3，k-平衡字符串是 "((()))"。

你必须 重复地 从 s 中移除所有 不重叠 的 k-平衡子串，然后将剩余部分连接起来。持续这个过程直到不存在 k-平衡 子串 为止。

返回所有可能的移除操作后的最终字符串。

子串 是字符串中 连续 的 非空 字符序列。


```
impl Solution {
    /// 返回移除所有不重叠的 k-平衡子串后的最终字符串
    ///
    /// # 算法思路
    /// 使用栈存储 `(字符, 连续出现次数)`：
    /// 1. 遍历每个字符，与栈顶比较：
    ///    - 如果相同，增加栈顶的计数
    ///    - 如果不同，压入新元素 `(字符, 1)`
    /// 2. 如果当前字符是 `)` 且栈顶恰好有 k 个连续的 `)`，
    ///    且栈顶下方有至少 k 个连续的 `(`，则进行抵消：
    ///    - 弹出右括号栈顶
    ///    - 左括号计数减少 k，若变为 0 则弹出
    ///
    /// # 复杂度
    /// - 时间复杂度：O(n)，每个字符最多入栈出栈一次
    /// - 空间复杂度：O(n)，栈空间
    pub fn remove_substring(s: String, k: i32) -> String {
        let k = k as usize;
        // 栈元素：(字符, 连续出现次数)
        let mut stack: Vec<(char, usize)> = Vec::with_capacity(s.len());

        for ch in s.chars() {
            // 处理当前字符
            if let Some((top_ch, top_count)) = stack.last_mut() {
                if *top_ch == ch {
                    // 与栈顶字符相同，增加计数
                    *top_count += 1;
                } else {
                    // 不同字符，压入新元素
                    stack.push((ch, 1));
                }
            } else {
                // 栈为空，压入新元素
                stack.push((ch, 1));
            }

            // 检查是否可以抵消：当前是 ')' 且栈顶有 k 个 ')'
            if ch == ')' && stack.len() >= 2 {
                let right_count = stack.last().unwrap().1;
                let left_count = stack[stack.len() - 2].1;

                // 栈顶恰好 k 个右括号，且下方左括号至少 k 个
                if right_count == k && left_count >= k {
                    // 弹出右括号
                    stack.pop();

                    // 减少左括号计数
                    if let Some((_, count)) = stack.last_mut() {
                        *count -= k;
                        if *count == 0 {
                            stack.pop(); // 左括号计数为 0，弹出
                        }
                    }
                }
            }
        }

        // 构建结果字符串
        let mut result = String::with_capacity(s.len());
        for (ch, count) in stack {
            result.extend(std::iter::repeat(ch).take(count));
        }
        result
    }
}
```
