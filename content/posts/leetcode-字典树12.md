---
title: "leetcode-字典树12"
date: 2026-06-02T10:23:07+08:00
tags: ["leetcode", "字典树"]
draft: false
---


## 前K个高频单词


给定一个单词列表 words 和一个整数 k ，返回前 k 个出现次数最多的单词。

返回的答案应该按单词出现频率由高到低排序。如果不同的单词有相同出现频率， 按字典顺序 排序。


```
impl Solution {
    /// 返回前 K 个高频单词
    ///
    /// # 参数
    /// - `words`: 单词数组
    /// - `k`: 需要返回的单词数量
    ///
    /// # 返回
    /// 按频率降序排列的前 K 个单词（频率相同按字母升序）
    ///
    /// # 示例
    /// ```
    /// let words = vec!["i".to_string(), "love".to_string(), "leetcode".to_string(),
    ///                  "i".to_string(), "love".to_string(), "coding".to_string()];
    /// let result = Solution::top_k_frequent(words, 2);
    /// assert_eq!(result, vec!["i".to_string(), "love".to_string()]);
    /// ```
    pub fn top_k_frequent(words: Vec<String>, k: i32) -> Vec<String> {
        // 导入所需的数据结构
        // BinaryHeap: 二叉堆（优先队列）
        // HashMap: 哈希映射（用于统计频率）
        use std::collections::{BinaryHeap, HashMap};
        // Reverse: 包装器，用于反转排序顺序（实现最小堆）
        use std::cmp::Reverse;

        // 步骤 1: 统计每个单词出现的频率
        // HashMap<String, usize> 存储: 单词 -> 出现次数
        let mut map = HashMap::<String, usize>::new();

        // 遍历所有单词，统计频率
        // into_iter() 消费 words 向量，取得所有权
        words
            .into_iter()
            // for_each 对每个单词执行闭包
            .for_each(|word| {
                // entry(word) 获取单词对应的条目
                // or_default() 如果不存在则插入默认值 0
                // += 1 将计数加 1
                // * 解引用操作符，修改 HashMap 中的值
                *map.entry(word).or_default() += 1
            });

        // 步骤 2: 将单词和频率放入二叉堆，实现排序
        // 排序规则：
        // - 主要按频率降序（高的在前）
        // - 频率相同时按字母升序（字典序）
        map.into_iter()  // 消费 map，取得 (单词, 频率) 的所有权
            // map 每个元素转换为 (Reverse(n), w)
            // Reverse(n) 的作用：BinaryHeap 默认是最大堆
            // 包装成 Reverse 后变成最小堆，配合 into_sorted_vec 得到升序
            // 然后反转得到降序，巧妙地实现了按频率降序排列
            .map(|(w, n)| (Reverse(n), w))
            // 收集到 BinaryHeap 中，自动按 (Reverse(n), w) 排序
            // 排序优先级：先比较 Reverse(n)（频率），再比较 w（单词）
            .collect::<BinaryHeap<_>>()
            // into_sorted_vec() 将堆转换为排序后的向量（升序）
            // 由于使用了 Reverse(n)，升序实际上是按频率升序
            .into_sorted_vec()
            // 转换为迭代器
            .into_iter()
            // 提取单词部分，丢弃 Reverse(n)
            .map(|(_, w)| w)
            // 只取前 k 个（take 需要 usize 类型）
            .take(k as usize)
            // 收集为 Vec<String> 作为返回值
            .collect()
    }
}
```
