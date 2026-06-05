---
title: "leetcode-递归19"
date: 2026-05-26T10:00:17+08:00
tags: ["leetcode", "递归"]
draft: false
---


## 字符串解码


给定一个经过编码的字符串，返回它解码后的字符串。

编码规则为: k[encoded_string]，表示其中方括号内部的 encoded_string 正好重复 k 次。注意 k 保证为正整数。

你可以认为输入字符串总是有效的；输入字符串中没有额外的空格，且输入的方括号总是符合格式要求的。

此外，你可以认为原始数据不包含数字，所有的数字只表示重复的次数 k ，例如不会出现像 3a 或 2[4] 的输入。

测试用例保证输出的长度不会超过 10^5。


```
impl Solution {
    /// 解码经过编码的字符串
    ///
    /// 编码规则：k[encoded_string] 表示 encoded_string 重复 k 次
    /// 例如： "3[a]2[bc]" -> "aaabcbc"
    ///       "3[a2[c]]" -> "accaccacc"
    ///
    /// 使用栈来解决嵌套问题
    pub fn decode_string(s: String) -> String {
        let mut stack = vec![];    // 栈：存储 (重复次数, 之前的字符串)
        let mut buf = vec![];      // 当前正在构建的字符串缓冲区（字节数组）
        let mut num = 0;           // 当前解析到的数字（重复次数）
        let s = s.as_bytes();      // 转换为字节数组，便于索引和匹配

        // 遍历输入字符串的每个字节
        for i in 0..s.len() {
            match s[i] {
                // 情况1：遇到数字字符（0-9）
                b'0'..=b'9' => {
                    // 累加数字（支持多位数，如 "12[a]"）
                    num = num * 10 + (s[i] - b'0') as usize;
                }

                // 情况2：遇到左括号 '['
                b'[' => {
                    // 将当前数字和缓冲区压入栈中，以便后续恢复
                    // std::mem::take(&mut buf) 会取出 buf 的所有权并替换为空 Vec
                    // 这比 clone 更高效（零拷贝）
                    stack.push((num, std::mem::take(&mut buf)));
                    num = 0;  // 重置数字，准备解析下一段
                }

                // 情况3：遇到右括号 ']'
                b']' => {
                    // 弹出栈顶元素（重复次数和之前的字符串）
                    if let Some((repeat, mut prev_buf)) = stack.pop() {
                        // 将当前的 buf 重复 repeat 次，追加到 prev_buf 后面
                        for _ in 0..repeat {
                            prev_buf.extend_from_slice(&buf);
                        }
                        // 更新 buf 为合并后的结果
                        buf = prev_buf;
                    }
                }

                // 情况4：遇到普通字母字符
                _ => {
                    // 直接添加到当前缓冲区
                    buf.push(s[i]);
                }
            }
        }

        // 安全转换：输入保证只包含 ASCII 字符（字母、数字、括号）
        // 因此 buf 中的字节序列是合法的 UTF-8
        unsafe { String::from_utf8_unchecked(buf) }
    }
}
```
