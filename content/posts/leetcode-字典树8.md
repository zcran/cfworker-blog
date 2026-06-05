---
title: "leetcode-字典树8"
date: 2026-06-02T10:23:07+08:00
tags: ["leetcode", "字典树"]
draft: false
---


## 连接词

给你一个 不含重复 单词的字符串数组 words ，请你找出并返回 words 中的所有 连接词 。

连接词 定义为：一个完全由给定数组中的至少两个较短单词（不一定是不同的两个单词）组成的字符串。

```
use std::collections::HashSet;

impl Solution {
    /// 找出字典中所有连接词（Concatenated Words）
    ///
    /// 连接词定义：一个单词完全由字典中其他至少两个单词拼接而成
    ///
    /// # 参数
    /// - `words`: 单词列表
    ///
    /// # 返回
    /// 所有连接词组成的向量
    ///
    /// # 示例
    /// ```
    /// let words = vec![
    ///     "cat".to_string(),
    ///     "cats".to_string(),
    ///     "catsdogcats".to_string(),
    ///     "dog".to_string(),
    ///     "dogcatsdog".to_string(),
    ///     "hippopotamuses".to_string(),
    ///     "rat".to_string(),
    ///     "ratcatdogcat".to_string()
    /// ];
    /// let result = Solution::find_all_concatenated_words_in_a_dict(words);
    /// // 结果: ["catsdogcats", "dogcatsdog", "ratcatdogcat"]
    /// // 解释:
    /// // - "catsdogcats" = "cats" + "dog" + "cats"
    /// // - "dogcatsdog" = "dog" + "cats" + "dog"
    /// // - "ratcatdogcat" = "rat" + "cat" + "dog" + "cat"
    /// ```
    ///
    /// # 算法核心
    /// 1. 使用哈希集合存储所有单词（快速 O(1) 查找）
    /// 2. 对每个单词，临时从集合中移除自身（避免使用自身作为组成部分）
    /// 3. 使用动态规划（DP）判断该单词是否能由其他单词拼接而成
    /// 4. 判断完成后，将单词放回集合供后续判断使用
    pub fn find_all_concatenated_words_in_a_dict(words: Vec<String>) -> Vec<String> {
        let mut set = HashSet::new();  // 存储所有单词的引用，用于快速查找
        let mut ret = Vec::new();       // 存储结果（连接词）

        // 步骤 1: 将所有单词放入哈希集合
        words.iter().for_each(|word| { set.insert(word); });

        // 步骤 2: 遍历每个单词，判断是否为连接词
        for word in words.iter() {
            // 跳过空字符串（如果存在）
            if "" == word { continue; }

            // 临时移除当前单词，避免使用自身作为组成部分
            // 例如：判断 "cat" 时，不能使用 "cat" 本身来拼接 "cat"
            set.remove(&word);

            // 检查当前单词是否能由集合中的其他单词拼接而成
            if can_break(&word, &set) {
                ret.push(word.clone());
            }

            // 恢复当前单词，供后续单词判断时使用
            set.insert(&word);
        }

        ret
    }
}

/// 判断字符串 s 是否能由字典 set 中的单词拼接而成
///
/// # 参数
/// - `s`: 要判断的字符串
/// - `set`: 单词字典（不包含 s 本身）
///
/// # 返回
/// - `true`: s 可以由 set 中的至少两个单词拼接而成
/// - `false`: s 不能由 set 中的单词拼接而成
///
/// # 算法：动态规划（完全背包问题）
///
/// 定义 dp[i] = 子串 s[0..i) 是否能被成功拼接
/// 状态转移方程：dp[i] = dp[j] && (s[j..i) in set) 对任意 j < i
///
/// 示例：s = "catsdog"
///      set = {"cat", "cats", "dog"}
///
/// dp[0] = true (空字符串)
/// dp[3] = dp[0] && "cat" in set = true
/// dp[4] = dp[0] && "cats" in set = true
/// dp[7] = dp[4] && "dog" in set = true
/// 最终 dp[7] = true，说明 "catsdog" 可拼接
fn can_break(s: &String, set: &HashSet<&String>) -> bool {
    let n = s.len();

    // 边界情况：字典为空或字符串为空，无法拼接
    if set.is_empty() || n == 0 {
        return false;
    }

    // dp[i] 表示子串 s[0..i) 是否可以由字典中的单词拼接而成
    let mut dp = vec![false; n + 1];
    dp[0] = true;  // 空字符串总是可拼接的

    // 动态规划填表
    // i 表示子串的结束位置（不包含）
    for i in 1..n + 1 {
        // j 表示子串的起始位置
        // 尝试将 s[0..i) 分割为 s[0..j) 和 s[j..i)
        for j in 0..i {
            // 如果前缀 s[0..j) 不可拼接，跳过
            if !dp[j] {
                continue;
            }

            // 检查后缀 s[j..i) 是否在字典中
            // 注意：to_string() 会创建新字符串，有一定开销
            // 但为了简洁性，这里直接使用
            if set.contains(&s[j..i].to_string()) {
                dp[i] = true;
                break;  // 一旦找到可行的分割点，立即停止
            }
        }
    }

    // 返回整个字符串是否可拼接
    dp[n]
}
```
