---
title: "leetcode-字典树13"
date: 2026-06-02T10:23:07+08:00
tags: ["leetcode", "字典树"]
draft: false
---


## 词典中最长的单词

给出一个字符串数组 words 组成的一本英语词典。返回能够通过 words 中其它单词逐步添加一个字母来构造得到的 words 中最长的单词。

若其中有多个可行的答案，则返回答案中字典序最小的单词。若无答案，则返回空字符串。

请注意，单词应该从左到右构建，每个额外的字符都添加到前一个单词的结尾。

```
impl Solution {
    /// 找到最长的单词，该单词可以通过逐个添加字母的方式从其他单词构建
    ///
    /// # 规则
    /// - 单词必须能够通过每次添加一个字母的方式从其他单词构建
    /// - 例如："a" -> "at" -> "cat" -> "chat" 是合法的
    /// - 如果有多个最长单词，返回字典序最小的
    ///
    /// # 参数
    /// - `words`: 单词列表
    ///
    /// # 返回
    /// 符合条件的单词，如果没有则返回空字符串
    ///
    /// # 示例
    /// ```
    /// let words = vec!["w".to_string(), "wo".to_string(), "wor".to_string(),
    ///                  "worl".to_string(), "world".to_string()];
    /// assert_eq!(Solution::longest_word(words), "world".to_string());
    /// ```
    pub fn longest_word(mut words: Vec<String>) -> String {
        // 导入 HashSet 用于快速查找
        use ::std::collections::HashSet;

        // 步骤 1: 对单词进行字典序排序
        // 这样可以确保当单词长度相同时，先处理字典序小的
        // 同时也方便后续处理（短单词会先出现）
        words.sort();

        // 步骤 2: 使用 fold 迭代器进行状态累积
        // 初始状态: ("", HashSet::new())
        //   - 第一个元素: 当前找到的最长单词（初始为空字符串）
        //   - 第二个元素: 所有可以构建的单词集合（用于快速查找前缀）
        words.iter().fold(("", HashSet::new()), |(ret, mut cache), word| {
            // 判断当前单词是否可以构建
            // 条件 1: 单词长度为 1（单个字符的单词总是可以构建）
            // 条件 2: 或者单词去掉最后一个字母后的前缀存在于缓存中
            if word.len() == 1 || cache.contains(&word[..word.len() - 1]) {
                // 如果可以构建，将当前单词加入缓存
                cache.insert(word.as_str());

                // 更新结果：如果当前单词比之前找到的更长，则替换
                // ret.len() < word.len() 使用长度比较，确保返回最长的
                // 注意：由于已经按字典序排序，长度相同时不会替换（保持字典序小的）
                (if ret.len() < word.len() { word } else { ret }, cache)
            } else {
                // 如果无法构建，保持结果不变
                (ret, cache)
            }
        // 取出 fold 的结果中的第一个元素（最长单词）
        }).0
        // 将 &str 转换为 String 类型返回
        .to_string()
    }
}
```
