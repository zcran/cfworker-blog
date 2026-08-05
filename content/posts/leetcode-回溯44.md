---
title: "leetcode-回溯44"
date: 2026-07-04T10:22:03+08:00
tags: ["leetcode", "回溯"]
draft: false
---


## 字母组合迭代器

请你设计一个迭代器类 CombinationIterator ，包括以下内容：

· CombinationIterator(string characters, int combinationLength) 一个构造函数，输入参数包括：用一个 有序且字符唯一 的字符串 characters（该字符串只包含小写英文字母）和一个数字 combinationLength 。

· 函数 next() ，按 字典序 返回长度为 combinationLength 的下一个字母组合。

· 函数 hasNext() ，只有存在长度为 combinationLength 的下一个字母组合时，才返回 true


```
struct CombinationIterator {
    chars: Vec<u8>,      // 排序后的字符数组
    indices: Vec<usize>, // 当前组合的索引位置
    has_next: bool,      // 是否还有下一个组合
    n: usize,            // 字符总数
    k: usize,            // 组合长度
}

impl CombinationIterator {
    fn new(characters: String, combination_length: i32) -> Self {
        let mut chars = characters.into_bytes();
        chars.sort_unstable(); // 确保字典序
        let n = chars.len();
        let k = combination_length as usize;

        Self {
            chars,
            indices: (0..k).collect(), // 初始组合：前 k 个字符
            has_next: true,
            n,
            k,
        }
    }

    fn next(&mut self) -> String {
        // 构建当前组合的字符串
        let result = self.indices.iter()
            .map(|&i| self.chars[i] as char)
            .collect::<String>();

        // 计算下一个组合
        self.advance();
        result
    }

    fn has_next(&self) -> bool {
        self.has_next
    }

    /// 将 indices 更新为下一个组合
    fn advance(&mut self) {
        // 从右向左查找可以递增的位置
        for i in (0..self.k).rev() {
            // 检查当前位置是否可以递增
            // 最大索引值 = self.n - self.k + i
            if self.indices[i] < self.n - self.k + i {
                // 递增当前位，并重置其右侧所有位
                self.indices[i] += 1;
                for j in i + 1..self.k {
                    self.indices[j] = self.indices[j - 1] + 1;
                }
                return;
            }
        }
        // 所有位置都无法递增，说明已到最后一个组合
        self.has_next = false;
    }
}
```
