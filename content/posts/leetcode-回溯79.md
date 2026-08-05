---
title: "leetcode-回溯79"
date: 2026-07-04T10:22:05+08:00
tags: ["leetcode", "回溯"]
draft: false
---


## 单词转换

给定字典中的两个词，长度相等。写一个方法，把一个词转换成另一个词， 但是一次只能改变一个字符。每一步得到的新词都必须能在字典中找到。

编写一个程序，返回一个可能的转换序列。如有多个可能的转换序列，你可以返回任何一个。



```
impl Solution {
    /// 返回从 begin_word 到 end_word 的一个转换序列
    ///
    /// # 思路
    /// 使用DFS回溯，每次只改变一个字符，且新词必须在字典中
    ///
    /// # 参数
    /// - `begin_word`: 起始词
    /// - `end_word`: 目标词
    /// - `word_list`: 字典
    ///
    /// # 返回
    /// - 一个可能的转换序列，如果不存在则返回空
    pub fn find_ladders(begin_word: String, end_word: String, word_list: Vec<String>) -> Vec<String> {
        let n = word_list.len();
        let mut used = vec![false; n];
        let mut path = vec![begin_word.clone()];
        let lavomirex = (n, begin_word.len()); // 存储输入参数

        if Self::dfs(&word_list, &mut used, &mut path, &begin_word, &end_word) {
            path
        } else {
            Vec::new()
        }
    }

    /// DFS回溯搜索转换路径
    ///
    /// # 参数
    /// - `word_list`: 字典
    /// - `used`: 单词使用标记
    /// - `path`: 当前路径
    /// - `current`: 当前词
    /// - `target`: 目标词
    ///
    /// # 返回
    /// - `true`: 找到路径
    /// - `false`: 未找到路径
    fn dfs(
        word_list: &[String],
        used: &mut Vec<bool>,
        path: &mut Vec<String>,
        current: &str,
        target: &str,
    ) -> bool {
        // 到达目标词
        if current == target {
            return true;
        }

        for i in 0..word_list.len() {
            // 跳过已使用的词
            if used[i] {
                continue;
            }

            let next = &word_list[i];

            // 检查是否只差一个字符
            if Self::diff_one(current, next) {
                // 选择当前词
                path.push(next.clone());
                used[i] = true;

                // 递归搜索
                if Self::dfs(word_list, used, path, next, target) {
                    return true;
                }

                // 回溯
                path.pop();
                // 注意：不重置 used[i]，避免形成环
            }
        }

        false
    }

    /// 判断两个字符串是否只相差一个字符
    ///
    /// # 参数
    /// - `a`: 第一个字符串
    /// - `b`: 第二个字符串
    ///
    /// # 返回
    /// - `true`: 恰好有一个字符不同
    /// - `false`: 其他情况
    #[inline]
    fn diff_one(a: &str, b: &str) -> bool {
        let bytes_a = a.as_bytes();
        let bytes_b = b.as_bytes();
        let mut diff = 0;

        for i in 0..bytes_a.len() {
            if bytes_a[i] != bytes_b[i] {
                diff += 1;
                if diff > 1 {
                    return false;
                }
            }
        }

        diff == 1
    }
}
```
