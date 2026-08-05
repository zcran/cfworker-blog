---
title: "leetcode-回溯22"
date: 2026-07-04T10:22:02+08:00
tags: ["leetcode", "回溯"]
draft: false
---


## 删除无效的括号


给你一个由若干括号和字母组成的字符串 s ，删除最小数量的无效括号，使得输入的字符串有效。

返回所有可能的结果。答案可以按 任意顺序 返回。

```
use std::collections::HashSet;

impl Solution {
    pub fn remove_invalid_parentheses(s: String) -> Vec<String> {
        let s = s.as_bytes();
        // 计算需要删除的最少左右括号数量
        let (rm_left, rm_right) = Self::count_invalid(s);

        let mut result_set = HashSet::new();
        let mut path = Vec::with_capacity(s.len());
        Self::dfs(s, 0, 0, rm_left, rm_right, &mut path, &mut result_set);

        // 如果结果集为空，但删除了所有括号（只有括号且全删）则添加空字符串
        // 但DFS应能处理，为保险起见
        if result_set.is_empty() && rm_left == 0 && rm_right > 0 {
            result_set.insert(String::new());
        }
        result_set.into_iter().collect()
    }

    /// 统计需要删除的最少左右括号数量
    fn count_invalid(s: &[u8]) -> (i32, i32) {
        let (mut rm_left, mut rm_right) = (0, 0);
        for &ch in s {
            match ch {
                b'(' => rm_left += 1,
                b')' => {
                    if rm_left > 0 {
                        rm_left -= 1;
                    } else {
                        rm_right += 1;
                    }
                }
                _ => {}
            }
        }
        (rm_left, rm_right)
    }

    /// 深度优先搜索所有有效结果
    fn dfs(
        s: &[u8],
        pos: usize,
        open: i32,
        rm_left: i32,
        rm_right: i32,
        path: &mut Vec<u8>,
        result: &mut HashSet<String>,
    ) {
        // 剪枝：剩余字符不足以删除需要的括号
        if rm_left + rm_right > (s.len() - pos) as i32 {
            return;
        }

        // 处理完所有字符
        if pos == s.len() {
            if rm_left == 0 && rm_right == 0 && open == 0 {
                result.insert(String::from_utf8(path.clone()).unwrap());
            }
            return;
        }

        let ch = s[pos];

        // 非括号字符直接保留
        if ch != b'(' && ch != b')' {
            path.push(ch);
            Self::dfs(s, pos + 1, open, rm_left, rm_right, path, result);
            path.pop();
            return;
        }

        // 尝试删除当前括号
        if ch == b'(' && rm_left > 0 {
            Self::dfs(s, pos + 1, open, rm_left - 1, rm_right, path, result);
        } else if ch == b')' && rm_right > 0 {
            Self::dfs(s, pos + 1, open, rm_left, rm_right - 1, path, result);
        }

        // 尝试保留当前括号
        if ch == b'(' {
            path.push(ch);
            Self::dfs(s, pos + 1, open + 1, rm_left, rm_right, path, result);
            path.pop();
        } else if ch == b')' && open > 0 {
            path.push(ch);
            Self::dfs(s, pos + 1, open - 1, rm_left, rm_right, path, result);
            path.pop();
        }
    }
}
```
