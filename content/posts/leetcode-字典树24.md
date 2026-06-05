---
title: "leetcode-字典树24"
date: 2026-06-02T10:23:07+08:00
tags: ["leetcode", "字典树"]
draft: false
---


## 字符串的前缀分数和


给你一个长度为 n 的数组 words ，该数组由 非空 字符串组成。

定义字符串 term 的 分数 等于以 term 作为 前缀 的 words[i] 的数目。

例如，如果 words = ["a", "ab", "abc", "cab"] ，那么 "ab" 的分数是 2 ，因为 "ab" 是 "ab" 和 "abc" 的一个前缀。

返回一个长度为 n 的数组 answer ，其中 answer[i] 是 words[i] 的每个非空前缀的分数 总和 。

注意：字符串视作它自身的一个前缀。

```
impl Solution {
    pub fn sum_prefix_scores(words: Vec<String>) -> Vec<i32> {
        // 题目：计算每个单词的所有前缀在所有单词中出现的次数之和
        // 例如：words = ["abc", "ab", "bc"]
        // "abc" 的前缀： "a","ab","abc"
        // "a" 在 "abc","ab" 中出现2次
        // "ab" 在 "abc","ab" 中出现2次
        // "abc" 在 "abc" 中出现1次
        // 所以 "abc" 的分数 = 2+2+1 = 5

        // 步骤1：为每个单词附加原始索引
        // enumerate() 得到 (索引, 单词) 的元组
        // collect 成 Vec<(usize, String)> 以便排序后还能找回原位置
        let mut words = words.into_iter().enumerate().collect::<Vec<_>>();

        // 步骤2：按字典序排序单词
        // 排序后，具有公共前缀的单词会相邻排列
        words.sort_by(|a, b| a.1.cmp(&b.1));

        // 步骤3：计算相邻单词之间的公共前缀长度
        // common_prefix_length[i] 表示 words[i-1] 和 words[i] 的公共前缀长度
        let mut common_prefix_length = vec![0; words.len()];
        for i in 1..words.len() {
            // 计算 words[i-1] 和 words[i] 的公共前缀
            common_prefix_length[i] = words[i - 1]
                .1                    // 前一个单词
                .chars()              // 转为字符迭代器
                .zip(words[i].1.chars())  // 与当前单词的字符配对
                .take_while(|(a, b)| a == b)  // 取相同字符直到不同
                .count() as i32;      // 计数并转为 i32
        }

        // 步骤4：计算每个单词的分数
        let mut ans = vec![0; words.len()];  // 存储最终答案（按原始索引）

        // 遍历所有单词（按排序后的顺序）
        for (i, (idx, word)) in words.iter().enumerate() {
            // 当前单词的所有前缀长度（从完整单词开始）
            let mut prefix_len = word.len() as i32;

            // 当前单词的完整单词本身作为前缀，贡献分数
            ans[*idx] += prefix_len;

            // 向右扫描，累加公共前缀的贡献
            // 原理：如果两个单词有公共前缀 L，那么这个前缀会对两个单词都产生贡献
            for j in i + 1..words.len() {
                // 更新当前能保持的最长公共前缀长度
                // 随着 j 增大，公共前缀长度只会减小或不变
                prefix_len = prefix_len.min(common_prefix_length[j]);

                // 如果没有公共前缀了，后续单词也不会有（因为已排序）
                if prefix_len == 0 {
                    break;
                }

                // 公共前缀长度为 prefix_len 时：
                // 1. 这些前缀都会出现在当前单词中
                // 2. 也会出现在 words[j] 中
                // 所以每个前缀对两个单词各贡献1分

                // 为当前单词（words[i]）增加分数
                ans[*idx] += prefix_len;
                // 为扫描到的单词（words[j]）增加分数
                ans[words[j].0] += prefix_len;
            }
        }

        // 返回按原始顺序的分数数组
        ans
    }
}
```
