---
title: "leetcode-栈48"
date: 2026-07-24T10:17:00+08:00
tags: ["leetcode", "栈"]
draft: false
---


## 使用机器人打印字典序最小的字符串

给你一个字符串 s 和一个机器人，机器人当前有一个空字符串 t 。执行以下操作之一，直到 s 和 t 都变成空字符串：

删除字符串 s 的 第一个 字符，并将该字符给机器人。机器人把这个字符添加到 t 的尾部。

删除字符串 t 的 最后一个 字符，并将该字符给机器人。机器人将该字符写到纸上。

请你返回纸上能写出的字典序最小的字符串。


```
impl Solution {
    /// 返回机器人操作后能写出的字典序最小的字符串
    ///
    /// # 算法思路
    /// 贪心策略 + 单调栈：
    /// 1. 统计字符串 s 中每个字符的剩余次数
    /// 2. 遍历 s 的每个字符，维护一个栈 t
    /// 3. 每次将当前字符压入栈后，更新剩余计数
    /// 4. 找到当前剩余字符中的最小字符（作为后续可能的最小值）
    /// 5. 只要栈顶字符 ≤ 剩余字符的最小值，就弹出栈顶写入结果
    ///    （因为此时弹出能保证当前字符是最优选择）
    ///
    /// # 复杂度
    /// - 时间复杂度：O(n * 26)，n 为字符串长度，但 26 是常数，实际 O(n)
    /// - 空间复杂度：O(n)，栈空间
    pub fn robot_with_string(s: String) -> String {
        // 统计 s 中每个字符的剩余次数
        let mut remaining = [0; 26];
        for ch in s.chars() {
            remaining[(ch as u8 - b'a') as usize] += 1;
        }

        let mut stack = Vec::with_capacity(s.len()); // 模拟 t 字符串
        let mut result = String::with_capacity(s.len());
        let mut min_char = b'a'; // 当前剩余字符中的最小字符

        for ch in s.chars() {
            // 1. 将当前字符压入栈
            stack.push(ch);

            // 2. 更新剩余计数
            let idx = (ch as u8 - b'a') as usize;
            remaining[idx] -= 1;

            // 3. 更新最小字符：跳过计数为 0 的字符
            while min_char != b'z' && remaining[(min_char - b'a') as usize] == 0 {
                min_char += 1;
            }

            // 4. 贪心弹出：若栈顶字符 ≤ 剩余字符的最小值，弹出写入结果
            while let Some(&top) = stack.last() {
                if top <= min_char as char {
                    result.push(stack.pop().unwrap());
                } else {
                    break;
                }
            }
        }

        result
    }
}
```
