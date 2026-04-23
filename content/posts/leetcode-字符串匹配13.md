---
title: "leetcode-字符串匹配13"
date: 2026-04-20T20:32:10+08:00
tags: ["leetcode", "字符串匹配"]
draft: false
---


## 替换字符后匹配

给你两个字符串 s 和 sub 。同时给你一个二维字符数组 mappings ，其中 mappings[i] = [oldi, newi] 表示你可以将 sub 中任意数目的 oldi 字符替换为 newi 。sub 中每个字符 不能 被替换超过一次。

如果使用 mappings 替换 0 个或者若干个字符，可以将 sub 变成 s 的一个子字符串，请你返回 true，否则返回 false 。

一个 子字符串 是字符串中连续非空的字符序列。


```
use std::collections::{HashMap, HashSet};

impl Solution {
    pub fn match_replacement(s: String, sub: String, mappings: Vec<Vec<char>>) -> bool {
        // ---------- 构建每个字符的可达集合 ----------
        // reachable[c] 表示字符 c 可以变成的所有字符的集合（包括自身）
        let mut reachable: HashMap<char, HashSet<char>> = HashMap::new();

        // 初始时，每个字符只能变为它自己
        // 从 sub 和 mappings 中收集所有可能出现的字符
        for ch in sub.chars().chain(mappings.iter().flat_map(|v| v.iter().copied())) {
            reachable.entry(ch).or_insert_with(HashSet::new).insert(ch);
        }

        // 应用映射规则：old -> new
        for mapping in mappings {
            let old = mapping[0];
            let new = mapping[1];
            // 确保 old 和 new 都已在 map 中（可能 new 是新字符）
            reachable.entry(old).or_insert_with(HashSet::new).insert(new);
            // 如果 new 之前未出现过，也初始化它的自环
            reachable.entry(new).or_insert_with(HashSet::new).insert(new);
        }

        let sub_chars: Vec<char> = sub.chars().collect();
        let sub_len = sub_chars.len();

        // 如果 sub 比 s 长，直接返回 false
        if sub_len > s.len() {
            return false;
        }

        // ---------- 在 s 的滑动窗口中查找匹配 ----------
        s.chars()
            .collect::<Vec<char>>()
            .windows(sub_len)                // 生成所有等长窗口
            .any(|window| {
                window.iter()
                    .zip(&sub_chars)
                    .all(|(&w, &b)| {
                        // 检查 w 是否在 b 的可达集合中
                        reachable
                            .get(&b)
                            .map(|set| set.contains(&w))
                            .unwrap_or(false)   // 若 b 不在 map 中（理论上不会发生），视为不匹配
                    })
            })
    }
}
```



修正点说明

1.字符集泛化
移除了对 ASCII 小写字母的硬编码假设，改用 HashMap<char, HashSet<char>> 存储可达关系。无论输入是何种 Unicode 字符，均可正确处理。

2.安全的索引访问
原代码中 (b - b'a') as usize 可能引发减法溢出或越界访问。新代码通过 HashMap::get 安全查询，并用 unwrap_or(false) 处理缺失情况。

3.函数式风格保持

使用 s.chars().collect::<Vec<char>>().windows(sub_len) 生成字符窗口（由于 str::windows 返回字节窗口，对 UTF-8 字符不安全，故先收集为 Vec<char>）。
链式调用 any 与 all，逻辑清晰，短路求值。

4.初始化完备性

从 sub 和 mappings 中收集所有出现的字符，确保每个字符的自环都被初始化。
即使 new 字符未在 sub 或 mappings 的其他地方出现，也会被自动加入并拥有自环。

性能考量

·时间复杂度：预处理 O(m + sub_len)，其中 m 为 mappings 长度；匹配过程 O(|s| * |sub|)，常数稍高但仍可接受（字符比较涉及哈希查找）。

·空间复杂度：O(字符种类数)，实际非常小。

若题目明确限制字符集为 ASCII（例如 128 个），也可采用 [u128; 128] 位掩码优化，但上述 HashMap 方案足够通用且符合 Rust 安全哲学。
