---
title: "leetcode-字典树40"
date: 2026-06-02T10:23:08+08:00
tags: ["leetcode", "字典树"]
draft: false
---

## 最长单词

给定一组单词words，编写一个程序，找出其中的最长单词，且该单词由这组单词中的其他单词组合而成。若有多个长度相同的结果，返回其中字典序最小的一项，若没有符合要求的单词则返回空字符串。


```
impl Solution {
    /// 找出可以由其他单词逐个字母拼接形成的最长单词
    ///
    /// 规则：一个单词可以由数组中的其他单词（可重复使用）逐个字母拼接而成
    /// 如果有多个长度相同的单词，返回字典序最小的那个
    ///
    /// # 算法思路
    /// 1. 按长度降序排列单词，这样长的单词排在前面
    /// 2. 遍历每个单词，检查它是否能由后面的单词（更短或等长的单词）拼接而成
    /// 3. 使用动态规划（完全背包问题）判断单词是否可拼接
    ///
    /// # 示例
    /// ```
    /// let words = vec!["a".to_string(), "banana".to_string(), "app".to_string(),
    ///                  "ap".to_string(), "p".to_string(), "ple".to_string()];
    /// let result = Solution::longest_word(words);
    /// assert_eq!(result, "apple");  // "apple" = "ap" + "ple"
    /// ```
    pub fn longest_word(words: Vec<String>) -> String {
        // 排序规则：
        // 1. 长度长的排在前面（因为我们要找最长的单词）
        // 2. 长度相同时，字典序小的排在前面（保证答案是最小字典序）
        let mut ws = words.clone();
        ws.sort_by(|a, b| {
            if a.len() != b.len() {
                // 长的在前：b.len() > a.len() 时返回 Ordering::Less
                b.len().cmp(&a.len())
            } else {
                // 字典序升序：a < b 时返回 Ordering::Less
                a.cmp(b)
            }
        });

        // 遍历排序后的单词，找到第一个可以被其他单词组成的
        for i in 0..ws.len() {
            let word = ws[i].clone();
            // 检查当前单词是否能由 i+1..len 范围内的单词组成
            if Self::check(&ws, i + 1, ws.len() - 1, word.clone()) {
                return word;
            }
        }

        // 没有找到符合条件的单词
        "".to_string()
    }

    /// 判断字符串是否可以由指定范围内的单词组成（完全背包问题）
    ///
    /// # 参数
    /// * `words` - 所有单词的列表
    /// * `l` - 可用单词的左边界（包含）
    /// * `r` - 可用单词的右边界（包含）
    /// * `str` - 要判断的目标字符串
    ///
    /// # 返回
    /// 如果能由范围内的单词组成（单词可重复使用），返回 true；否则返回 false
    ///
    /// # 算法说明
    /// 这是典型的完全背包问题变种：
    /// - 背包容量：字符串长度 n
    /// - 物品：范围内的单词
    /// - 状态：dp[i] 表示前 i 个字符能否由单词组成
    /// - 状态转移：dp[i] = dp[i] || dp[i - word.len()] (如果末尾匹配)
    ///
    /// # 复杂度
    /// * 时间：O(n * m)，其中 n 为目标字符串长度，m 为单词范围大小
    /// * 空间：O(n)
    fn check(words: &Vec<String>, l: usize, r: usize, str: String) -> bool {
        let n = str.len();
        // dp[i]：前 i 个字符能否由单词组成
        let mut dp = vec![false; n + 1];

        // 空字符串可以由 0 个单词组成
        dp[0] = true;

        // 遍历字符串的每个位置（背包容量）
        for i in 1..=n {
            // 遍历所有可用的单词（物品）
            for j in l..=r {
                let word = &words[j];
                let len = word.len();

                // 如果能装下这个单词 && 末尾字符与单词匹配
                if len <= i && &str[i - len..i] == word {
                    // 状态转移：如果去掉这个单词的前缀可组成，则当前位置也可组成
                    dp[i] = dp[i] || dp[i - len];
                }
            }
        }

        dp[n]
    }
}
```
