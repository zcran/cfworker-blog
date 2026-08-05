---
title: "leetcode-回溯42"
date: 2026-07-04T10:22:03+08:00
tags: ["leetcode", "回溯"]
draft: false
---


## 串联字符串的最大长度

给定一个字符串数组 arr，字符串 s 是将 arr 的含有 不同字母 的 子序列 字符串 连接 所得的字符串。

请返回所有可行解 s 中最长长度。

子序列 是一种可以从另一个数组派生而来的数组，通过删除某些元素或不删除元素而不改变其余元素的顺序。




```
impl Solution {
    pub fn max_length(arr: Vec<String>) -> i32 {
        // 将字符串转换为位掩码，只保留有效的（无重复字符）
        let masks: Vec<i32> = arr
            .iter()
            .filter_map(|s| {
                let mut mask = 0;
                for &ch in s.as_bytes() {
                    let bit = 1 << (ch - b'a');
                    if mask & bit != 0 {
                        return None; // 有重复字符，跳过
                    }
                    mask |= bit;
                }
                Some(mask)
            })
            .collect();

        // DFS 回溯：每个字符串选或不选
        fn dfs(masks: &[i32], idx: usize, current_mask: i32) -> i32 {
            if idx == masks.len() {
                return current_mask.count_ones() as i32;
            }
            // 不选当前字符串
            let mut max_len = dfs(masks, idx + 1, current_mask);
            // 选当前字符串（如果没有重复字符）
            if current_mask & masks[idx] == 0 {
                max_len = max_len.max(dfs(masks, idx + 1, current_mask | masks[idx]));
            }
            max_len
        }

        dfs(&masks, 0, 0)
    }
}
```
