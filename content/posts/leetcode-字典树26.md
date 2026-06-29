---
title: "leetcode-字典树26"
date: 2026-06-02T10:23:07+08:00
tags: ["leetcode", "字典树"]
draft: false
---


## 字符串中的额外字符

给你一个下标从 0 开始的字符串 s 和一个单词字典 dictionary 。你需要将 s 分割成若干个 互不重叠 的子字符串，每个子字符串都在 dictionary 中出现过。s 中可能会有一些 额外的字符 不在任何子字符串中。

请你采取最优策略分割 s ，使剩下的字符 最少 。

```

// 导入标准库中的哈希表
use std::collections::HashMap;

// ========== Trie（前缀树/字典树）结构定义 ==========
struct Trie {
    mark: bool,                    // 标记当前节点是否为一个单词的结尾
    nxt: HashMap<u8, Box<Trie>>,   // 子节点映射：字节 -> 子节点（使用Box在堆上分配）
}

impl Trie {
    // 创建一个新的Trie节点
    fn new() -> Self {
        Self {
            mark: false,
            nxt: HashMap::new(),
        }
    }

    // 向Trie中插入一个单词（注意：逆序插入）
    // 参数：s - 单词的字节数组
    fn insert(&mut self, s: Vec<u8>) {
        let mut ptr = self;
        // 关键：逆序遍历单词的字节
        // 例如单词 "abc" 会按 c -> b -> a 的顺序插入
        for x in s.into_iter().rev() {
            // entry 获取或创建子节点
            // or_insert 如果不存在则插入新节点
            ptr = ptr.nxt.entry(x).or_insert(Box::new(Trie {
                mark: false,
                nxt: HashMap::new(),
            }));
        }
        // 标记单词结尾
        ptr.mark = true;
    }

    // 查询：找出所有以当前字符串结尾的单词，并返回这些单词的长度
    // 参数：s - 待查询的字符串（字节数组）
    // 返回值：所有匹配的单词长度列表
    fn query(&self, s: &Vec<u8>) -> Vec<usize> {
        let mut ptr = self;
        let mut res = vec![];

        // 逆序遍历字符串，查找匹配的单词
        // 例如 s = "abc"，会按 c -> b -> a 的顺序在Trie中查找
        for (i, &x) in s.iter().rev().enumerate() {
            // 如果当前节点是一个单词结尾，说明找到了一个匹配
            // 这个匹配的单词长度就是 i（因为逆序匹配的长度）
            if ptr.mark {
                res.push(i);
            }

            // 继续向下查找
            if let Some(p) = ptr.nxt.get(&x) {
                ptr = p.as_ref();
            } else {
                // 无匹配，提前结束
                return res;
            }
        }

        // 检查最后一个节点（整个字符串匹配完）
        if ptr.mark {
            res.push(s.len());
        }

        res
    }
}

impl Solution {
    // 主函数：计算删除的最小字符数，使得剩余字符可以分割成字典中的单词
    // 问题：给定字符串 s 和字典，可以删除任意字符，求最少删除多少个字符后，
    //       剩余字符可以分割成若干个字典中的单词（单词可以重叠？不，是连续分割）
    // 实际这是"将字符串分割成字典单词"的变种，求最少未匹配的字符数
    pub fn min_extra_char(s: String, dictionary: Vec<String>) -> i32 {
        // 将字符串转为字节数组
        let s = s.into_bytes();
        let n = s.len();  // 字符串长度

        // 构建Trie，插入所有字典单词（逆序存储）
        let mut trie = Trie::new();
        for word in dictionary {
            trie.insert(word.into_bytes());
        }

        // dp[i] 表示处理前 i 个字符（s[0..i]）的最小额外字符数
        // 额外字符：无法被字典单词匹配的字符
        let mut dp = vec![0; n + 1];

        // query_string 动态构建，从第一个字符开始累积
        let mut query_string = vec![];

        // 动态规划，遍历每个位置
        for i in 1..=n {
            // 将当前字符加入查询字符串
            query_string.push(s[i - 1]);

            // 查询以当前位置结尾的所有字典单词的长度
            // 这些单词都是 query_string 的后缀
            let matchings = trie.query(&query_string);

            // 情况1：不使用任何匹配的单词，直接丢弃第 i 个字符
            // 遍历所有可能的前一个分割点 j，取最小值
            // 状态转移：dp[i] = min(dp[j] + (i - j))，其中 (i-j) 是未匹配的字符数
            dp[i] = (0..i).fold(usize::MAX, |c, j| {
                c.min(i - j + dp[j])  // i-j 个额外字符 + 前 j 个字符的最优解
            });

            // 情况2：如果当前位置能匹配到字典单词，尝试使用这些单词
            // 对于每个匹配到的单词长度 len，可以 dp[i-len] + 0 个额外字符
            // 因为单词本身完全匹配，不需要删除
            dp[i] = dp[i].min(
                matchings.into_iter().fold(usize::MAX, |c, j| {
                    c.min(dp[i - j])  // j 是单词长度，不产生额外字符
                })
            );
        }

        // 返回处理完整个字符串的最小额外字符数
        dp[n] as i32
    }
}
```
