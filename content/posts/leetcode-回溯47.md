---
title: "leetcode-回溯47"
date: 2026-07-04T10:22:03+08:00
tags: ["leetcode", "回溯"]
draft: false
---


## 拆分字符串使唯一子字符串的数目最大

给你一个字符串 s ，请你拆分该字符串，并返回拆分后唯一子字符串的最大数目。

字符串 s 拆分后可以得到若干 非空子字符串 ，这些子字符串连接后应当能够还原为原字符串。但是拆分出来的每个子字符串都必须是 唯一的 。

注意：子字符串 是字符串中的一个连续字符序列。


```
use std::collections::HashSet;

impl Solution {
    pub fn max_unique_split(s: String) -> i32 {
        let chars: Vec<char> = s.chars().collect();
        let mut used = HashSet::new();
        let mut max_count = 0;
        Self::backtrack(&chars, 0, 0, &mut used, &mut max_count);
        max_count as i32
    }

    /// 回溯搜索
    /// - chars: 字符数组
    /// - start: 当前切割起始位置
    /// - count: 已切割的子串数量
    /// - used: 已使用的子串集合
    /// - max_count: 最大子串数量
    fn backtrack(
        chars: &[char],
        start: usize,
        count: usize,
        used: &mut HashSet<String>,
        max_count: &mut usize,
    ) {
        // 剪枝：即使剩余每个字符都单独切割，也无法超过当前最优
        if count + (chars.len() - start) <= *max_count {
            return;
        }

        // 到达末尾，更新最优解
        if start == chars.len() {
            *max_count = (*max_count).max(count);
            return;
        }

        // 尝试从 start 开始的所有可能的子串
        let mut sub = String::new();
        for end in start..chars.len() {
            sub.push(chars[end]);

            // 如果子串已使用，跳过
            if used.contains(&sub) {
                continue;
            }

            // 选择当前子串
            used.insert(sub.clone());
            Self::backtrack(chars, end + 1, count + 1, used, max_count);
            used.remove(&sub);
        }
    }
}
```
