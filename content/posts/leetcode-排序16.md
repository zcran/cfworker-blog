---
title: "leetcode-排序16"
date: 2026-10-05T00:35:00+08:00
tags: ["leetcode", "排序"]
draft: false
---


## 字符串中的查找与替换

你会得到一个字符串 s (索引从 0 开始)，你必须对它执行 k 个替换操作。替换操作以三个长度均为 k 的并行数组给出：indices, sources,  targets。

要完成第 i 个替换操作:

1. 检查 子字符串  sources[i] 是否出现在 原字符串 s 的索引 indices[i] 处。
2. 如果没有出现， 什么也不做 。
3. 如果出现，则用 targets[i] 替换 该子字符串。

例如，如果 s = "abcd" ， indices[i] = 0 , sources[i] = "ab"， targets[i] = "eee" ，那么替换的结果将是 "eeecd" 。

所有替换操作必须 同时 发生，这意味着替换操作不应该影响彼此的索引。测试用例保证元素间不会重叠 。

· 例如，一个 s = "abc" ，  indices = [0,1] ， sources = ["ab"，"bc"] 的测试用例将不会生成，因为 "ab" 和 "bc" 替换重叠。

在对 s 执行所有替换操作后返回 结果字符串 。

子字符串 是字符串中连续的字符序列。


```
impl Solution {
    /// 同时执行所有替换操作后返回结果字符串
    ///
    /// 关键点：所有替换必须「同时」发生，因此匹配判断必须基于原始字符串 s。
    /// 做法：先遍历所有操作，基于原始 s 筛选出真正匹配的操作；
    /// 再按起始索引从大到小排序，从右向左依次替换，
    /// 这样右侧替换不会影响左侧索引，天然满足「同时」语义。
    /// 题目保证有效操作之间不重叠。
    pub fn find_replace_string(
        mut s: String,
        indices: Vec<i32>,
        sources: Vec<String>,
        targets: Vec<String>,
    ) -> String {
        // 收集所有在原始字符串中匹配成功的操作：(起始位置, 操作序号)
        let mut valid_ops: Vec<(usize, usize)> = Vec::new();
        for (i, &idx) in indices.iter().enumerate() {
            let start = idx as usize;
            let source = &sources[i];
            let end = start + source.len();
            // 基于原始 s 检查匹配，确保同时替换的语义
            if end <= s.len() && &s[start..end] == source.as_str() {
                valid_ops.push((start, i));
            }
        }

        // 按起始位置从大到小排序，从右向左替换，避免索引偏移
        valid_ops.sort_unstable_by_key(|&(start, _)| std::cmp::Reverse(start));

        for (start, i) in valid_ops {
            let source = &sources[i];
            let end = start + source.len();
            s.replace_range(start..end, &targets[i]);
        }

        s
    }
}
```
