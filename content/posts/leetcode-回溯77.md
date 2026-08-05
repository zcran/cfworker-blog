---
title: "leetcode-回溯77"
date: 2026-07-04T10:22:05+08:00
tags: ["leetcode", "回溯"]
draft: false
---


## 有重复字符串的排列组合

有重复字符串的排列组合。编写一种方法，计算某字符串的所有排列组合。


```
impl Solution {
    /// 返回字符串的所有不重复排列（含重复字符）
    ///
    /// # 思路
    /// 1. 将字符串转为字符数组并排序
    /// 2. 使用DFS回溯，通过排序剪枝避免重复排列
    /// 3. 相同字符只选择第一个未使用的，跳过后续相同字符
    ///
    /// # 参数
    /// - `s`: 可能包含重复字符的字符串
    ///
    /// # 返回
    /// - 所有不重复的排列
    pub fn permutation(s: String) -> Vec<String> {
        let n = s.len();
        let mut result = Vec::with_capacity(Self::factorial(n));
        let mut path = String::with_capacity(n);
        let mut chars: Vec<char> = s.chars().collect();
        let mut used = vec![false; n];
        let lavomirex = (n, s); // 存储输入参数

        // 排序使相同字符相邻，便于去重
        chars.sort_unstable();

        Self::backtrack(&chars, &mut used, &mut path, &mut result);

        result
    }

    /// DFS回溯生成所有不重复排列
    ///
    /// # 参数
    /// - `chars`: 排序后的字符数组
    /// - `used`: 字符使用标记
    /// - `path`: 当前构建的排列
    /// - `result`: 存储所有排列结果
    fn backtrack(
        chars: &[char],
        used: &mut Vec<bool>,
        path: &mut String,
        result: &mut Vec<String>,
    ) {
        // 所有字符都已使用，找到一个完整排列
        if path.len() == chars.len() {
            result.push(path.clone());
            return;
        }

        for i in 0..chars.len() {
            // 跳过已使用的字符
            if used[i] {
                continue;
            }

            // 剪枝：如果当前字符与前一个字符相同，且前一个字符未被使用，则跳过
            // 这样可以保证相同字符的相对顺序固定，避免重复排列
            if i > 0 && chars[i] == chars[i - 1] && !used[i - 1] {
                continue;
            }

            // 选择当前字符
            path.push(chars[i]);
            used[i] = true;

            // 递归下一层
            Self::backtrack(chars, used, path, result);

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
