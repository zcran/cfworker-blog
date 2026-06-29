---
title: "leetcode-单调栈4"
date: 2026-06-07T10:14:04+08:00
tags: ["leetcode", "单调栈"]
draft: false
---


## 去除重复字母

给你一个字符串 s ，请你去除字符串中重复的字母，使得每个字母只出现一次。需保证 返回结果的字典序最小（要求不能打乱其他字符的相对位置）。

```
impl Solution {
    pub fn remove_duplicate_letters(s: String) -> String {
        let size = s.len();  // 获取字符串长度（虽然实际未使用）

        let mut last_appear_index = [0; 26];  // 记录每个字母最后一次出现的位置
        let mut if_in_stack = [false; 26];    // 记录每个字母是否已在栈中

        // 遍历字符串，记录每个字符最后一次出现的索引
        for (n, i) in s.bytes().enumerate() {
            // 将字母转换为索引（0-25），存储其最后出现位置
            last_appear_index[(i - b'a') as usize] = n as i16;
        }

        let mut stack = vec![b'a'];  // 初始化栈，放入哨兵字符（比'a'小）

        // 再次遍历字符串，构建单调递增栈
        for (n, i) in s.bytes().enumerate() {
            // 如果当前字符已经在栈中，直接跳过
            if if_in_stack[(i - b'a') as usize] {
                continue;
            }

            // 当栈非空时，检查是否需要弹出栈顶元素
            while let Some(s) = stack.pop() {
                // 条件：栈顶字符 > 当前字符，且栈顶字符后面还会出现
                if s > i && last_appear_index[(s - b'a') as usize] > n as i16 {
                    // 弹出栈顶，标记为不在栈中
                    if_in_stack[(s - b'a') as usize] = false;
                } else {
                    // 不满足条件，将栈顶放回
                    stack.push(s);
                    break;
                }
            }

            // 将当前字符压入栈，并标记为已在栈中
            stack.push(i);
            if_in_stack[(i - b'a') as usize] = true;
        }

        // 将栈转换为字符串（跳过哨兵字符）
        stack.drain(1..).map(|x| x as char).collect()
    }
}
```
