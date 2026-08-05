---
title: "leetcode-回溯30"
date: 2026-07-04T10:22:03+08:00
tags: ["leetcode", "回溯"]
draft: false
---


## 字母大小写全排列

给定一个字符串 s ，通过将字符串 s 中的每个字母转变大小写，我们可以获得一个新的字符串。

返回 所有可能得到的字符串集合 。以 任意顺序 返回输出。


```
impl Solution {
    /// 返回字符串所有可能的大小写组合
    ///
    /// 思路：DFS 回溯，遇到字母则分叉为大小写两种，数字保持不变
    pub fn letter_case_permutation(s: String) -> Vec<String> {
        let mut result = Vec::new();
        let mut chars: Vec<char> = s.chars().collect();

        fn backtrack(chars: &mut [char], idx: usize, result: &mut Vec<String>) {
            // 到达末尾，记录当前组合
            if idx == chars.len() {
                result.push(chars.iter().collect());
                return;
            }

            let c = chars[idx];

            if c.is_ascii_digit() {
                // 数字：直接跳过
                backtrack(chars, idx + 1, result);
            } else {
                // 字母：尝试大写
                chars[idx] = c.to_ascii_uppercase();
                backtrack(chars, idx + 1, result);

                // 字母：尝试小写
                chars[idx] = c.to_ascii_lowercase();
                backtrack(chars, idx + 1, result);

                // 恢复原字符（虽然不恢复也能工作，但为保持良好习惯）
                chars[idx] = c;
            }
        }

        backtrack(&mut chars, 0, &mut result);
        result
    }
}
```
