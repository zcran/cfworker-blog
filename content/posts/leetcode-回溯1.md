---
title: "leetcode-回溯1"
date: 2026-07-04T10:22:01+08:00
tags: ["leetcode", "回溯"]
draft: false
---


## 电话号码的字母组合

给定一个仅包含数字 2-9 的字符串，返回所有它能表示的字母组合。答案可以按 任意顺序 返回。

给出数字到字母的映射如下（与电话按键相同）。注意 1 不对应任何字母。

![](../pic/1752723054-mfIHZs-image.png)


```
impl Solution {
    /// 电话号码的字母组合
    ///
    /// 给定一个仅包含数字 2-9 的字符串，返回所有可能的字母组合
    ///
    /// # 映射关系
    /// 2 -> abc, 3 -> def, 4 -> ghi, 5 -> jkl,
    /// 6 -> mno, 7 -> pqrs, 8 -> tuv, 9 -> wxyz
    ///
    /// # 示例
    /// ```
    /// 输入: "23"
    /// 输出: ["ad", "ae", "af", "bd", "be", "bf", "cd", "ce", "cf"]
    /// ```
    ///
    /// # 复杂度
    /// - 时间复杂度: O(4^n)，n 为 digits 长度
    /// - 空间复杂度: O(4^n)
    pub fn letter_combinations(digits: String) -> Vec<String> {
        // 边界处理：空字符串返回空向量
        if digits.is_empty() {
            return Vec::new();
        }

        // 电话键盘映射（索引 0-9，索引 0 和 1 为空）
        const KEYBOARD: [&str; 10] = [
            "", "", "abc", "def", "ghi", "jkl",
            "mno", "pqrs", "tuv", "wxyz"
        ];

        // 使用动态规划构建所有组合
        let mut result = vec![String::new()];

        for ch in digits.chars() {
            // 获取当前数字对应的字母集
            let letters = KEYBOARD[ch.to_digit(10).unwrap() as usize];
            let mut new_result = Vec::with_capacity(result.len() * letters.len());

            // 组合当前字母与已有结果
            for prefix in &result {
                for c in letters.chars() {
                    let mut s = prefix.clone();
                    s.push(c);
                    new_result.push(s);
                }
            }

            result = new_result;
        }

        result
    }
}
```
