---
title: "leetcode-字典树25"
date: 2026-06-02T10:23:07+08:00
tags: ["leetcode", "字典树"]
draft: false
---


## 距离字典两次编辑以内的单词

给你两个字符串数组 queries 和 dictionary 。数组中所有单词都只包含小写英文字母，且长度都相同。

一次 编辑 中，你可以从 queries 中选择一个单词，将任意一个字母修改成任何其他字母。从 queries 中找到所有满足以下条件的字符串：不超过 两次编辑内，字符串与 dictionary 中某个字符串相同。

请你返回 queries 中的单词列表，这些单词距离 dictionary 中的单词 编辑次数 不超过 两次 。单词返回的顺序需要与 queries 中原本顺序相同。

```
impl Solution {
    // 题目：找出所有满足条件的 queries 中的单词
    // 条件：该单词与 dictionary 中至少一个单词的编辑距离 ≤ 2
    // 这里的编辑距离定义为：相同位置上不同字符的数量（汉明距离）
    pub fn two_edit_words(queries: Vec<String>, dictionary: Vec<String>) -> Vec<String> {
        // 存储符合条件的查询单词
        let mut ans = Vec::new();

        // 遍历每一个查询单词
        for query in &queries {
            // 遍历词典中的每一个单词，检查是否有符合条件的
            for s in &dictionary {
                // 记录当前位置字符不同的数量
                let mut dis = 0;

                // 将查询单词和词典单词按字符位置逐个比较
                // zip 会将两个字符串的字符配对，当任一字符串结束时停止
                for (c1, c2) in query.chars().zip(s.chars()) {
                    if c1 != c2 {
                        dis += 1;  // 字符不同，距离加1
                    }
                }

                // 如果编辑距离 ≤ 2，说明符合条件
                if dis <= 2 {
                    ans.push(query.clone());  // 将查询单词加入答案
                    break;  // 找到一个符合条件的就停止检查该单词
                }
            }
        }

        ans
    }
}

```
