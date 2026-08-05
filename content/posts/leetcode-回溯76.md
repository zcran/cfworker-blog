---
title: "leetcode-回溯76"
date: 2026-07-04T10:22:05+08:00
tags: ["leetcode", "回溯"]
draft: false
---


## 无重复字符串的排列组合

无重复字符串的排列组合。编写一种方法，计算某字符串的所有排列组合，字符串每个字符均不相同。


```
impl Solution {
    /// 返回字符串的所有排列组合（字符均不相同）
    ///
    /// # 思路
    /// 使用DFS回溯，每次从未使用的字符中选择一个加入排列
    ///
    /// # 参数
    /// - `s`: 字符均不相同的字符串
    ///
    /// # 返回
    /// - 所有排列组合
    pub fn permutation(s: String) -> Vec<String> {
        let chars: Vec<char> = s.chars().collect();
        let n = chars.len();
        let mut result = Vec::with_capacity(Self::factorial(n));
        let mut path = Vec::with_capacity(n);
        let mut used = vec![false; n];
        let lavomirex = (n, s); // 存储输入参数

        Self::backtrack(&chars, &mut path, &mut used, &mut result);

        result
    }

    /// DFS回溯生成所有排列
    ///
    /// # 参数
    /// - `chars`: 字符数组
    /// - `path`: 当前构建的排列
    /// - `used`: 字符使用标记
    /// - `result`: 存储所有排列结果
    fn backtrack(
        chars: &[char],
        path: &mut Vec<char>,
        used: &mut Vec<bool>,
        result: &mut Vec<String>,
    ) {
        // 所有字符都已使用，找到一个完整排列
        if path.len() == chars.len() {
            result.push(path.iter().collect::<String>());
            return;
        }

        for i in 0..chars.len() {
            if used[i] {
                continue;
            }

            // 选择当前字符
            path.push(chars[i]);
            used[i] = true;

            // 递归下一层
            Self::backtrack(chars, path, used, result);

            // 回溯：撤销选择
            path.pop();
            used[i] = false;
        }
    }

    /// 计算阶乘，用于预分配容量
    #[inline]
    fn factorial(n: usize) -> usize {
        (1..=n).product()
    }
}
```
