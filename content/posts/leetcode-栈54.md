---
title: "leetcode-栈54"
date: 2026-07-24T10:17:00+08:00
tags: ["leetcode", "栈"]
draft: false
---


##  删除星号以后字典序最小的字符串

给你一个字符串 s 。它可能包含任意数量的 '*' 字符。你的任务是删除所有的 '*' 字符。

当字符串还存在至少一个 '*' 字符时，你可以执行以下操作：

删除最左边的 '*' 字符，同时删除该星号字符左边一个字典序 最小 的字符。如果有多个字典序最小的字符，你可以删除它们中的任意一个。

请你返回删除所有 '*' 字符以后，剩余字符连接而成的 字典序最小 的字符串。


```
impl Solution {
    /// 删除所有 '*' 字符，并删除每个 '*' 左侧字典序最小的非星号字符，
    /// 返回操作后字典序最小的字符串
    ///
    /// # 算法思路
    /// 1. 遍历字符串，记录每个字母出现的位置（按字母顺序存储索引）
    /// 2. 遇到 '*' 时，从 'a' 到 'z' 查找最小字母的最近位置并删除
    /// 3. 最后将所有被删除的位置标记为 '*'，过滤后得到结果
    ///
    /// # 复杂度
    /// - 时间复杂度：O(n * 26)，但 26 是常数，实际 O(n)
    /// - 空间复杂度：O(n)，存储位置索引
    pub fn clear_stars(s: String) -> String {
        let chars: Vec<char> = s.chars().collect();
        let n = chars.len();

        // 存储每个字母出现的位置（按从左到右的顺序）
        let mut positions: [Vec<usize>; 26] = std::array::from_fn(|_| Vec::new());

        // 记录需要删除的字符位置
        let mut to_remove = Vec::with_capacity(n);

        // 遍历字符串，记录字符位置，遇到 '*' 时处理删除
        for (i, &ch) in chars.iter().enumerate() {
            if ch == '*' {
                // 从 'a' 到 'z' 查找字典序最小的字符
                for letter in 0..26 {
                    if let Some(pos) = positions[letter].pop() {
                        // 删除该字符（记录位置）
                        to_remove.push(pos);
                        break;
                    }
                }
            } else {
                // 记录非星号字符的位置
                let idx = (ch as u8 - b'a') as usize;
                positions[idx].push(i);
            }
        }

        // 标记被删除的位置
        let mut result_chars = chars;
        for pos in to_remove {
            result_chars[pos] = '*';
        }

        // 过滤掉所有星号，得到最终结果
        result_chars.into_iter().filter(|&ch| ch != '*').collect()
    }
}
```
