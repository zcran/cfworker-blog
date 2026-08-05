---
title: "leetcode-回溯66"
date: 2026-07-04T10:22:04+08:00
tags: ["leetcode", "回溯"]
draft: false
---


## 生成不含相邻零的二进制字符串

给你一个正整数 n。

如果一个二进制字符串 x 的所有长度为 2 的子字符串中包含 至少 一个 "1"，则称 x 是一个 有效 字符串。

返回所有长度为 n 的 有效 字符串，可以以任意顺序排列。




```
impl Solution {
    pub fn valid_strings(n: i32) -> Vec<String> {
        let mut result = Vec::new();
        let mut path = Vec::with_capacity(n as usize);
        dfs(n as usize, &mut path, &mut result);
        result
    }
}

fn dfs(n: usize, path: &mut Vec<char>, result: &mut Vec<String>) {
    if path.len() == n {
        result.push(path.iter().collect());
        return;
    }

    // 如果路径为空或最后一个字符是 '1'，可以添加 '0'
    if path.is_empty() || path[path.len() - 1] == '1' {
        path.push('0');
        dfs(n, path, result);
        path.pop();
    }

    // 总是可以添加 '1'
    path.push('1');
    dfs(n, path, result);
    path.pop();
}
```
