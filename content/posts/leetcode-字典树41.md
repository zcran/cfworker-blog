---
title: "leetcode-字典树41"
date: 2026-06-02T10:23:08+08:00
tags: ["leetcode", "字典树"]
draft: false
---


## 单词矩阵

给定一份单词的清单，设计一个算法，创建由字母组成的面积最大的矩形，其中每一行组成一个单词(自左向右)，每一列也组成一个单词(自上而下)。不要求这些单词在清单里连续出现，但要求所有行等长，所有列等高。

如果有多个面积最大的矩形，输出任意一个均可。一个单词可以重复使用。

```
use std::collections::HashMap;  // 用于存储子节点映射
use std::borrow::BorrowMut;     // 用于获取 entry 值的可变引用

// Trie（前缀树）节点结构
struct Trie {
    children: HashMap<u8, Trie>,  // 当前节点的子节点，按字节映射
    word: String                   // 若当前节点是单词结尾则存储完整单词，否则为空
}

impl Trie {
    // 创建一个新的空 Trie 节点
    pub fn new() -> Trie {
        Trie{children: HashMap::new(), word: "".into()}
    }

    // 向 Trie 中插入一个单词
    pub fn insert(&mut self, word: &str) {
        let mut node = self;
        // 遍历单词的每个字节
        for b in word.as_bytes().iter().copied() {
            // 获取或创建当前字节对应的子节点，然后移动到该节点
            let mut entry = node.children.entry(b).or_insert(Trie::new());
            node = entry.borrow_mut();  // 可变借用以便继续向下遍历
        }
        node.word = word.into();  // 在单词结尾节点记录完整单词
    }

    // 查找给定字节对应的子节点（用于前缀匹配）
    pub fn find(&self, ch: u8) -> Option<&Trie> {
        self.children.get(&ch)
    }
}

impl Solution {
    // 主函数：找出能形成的最大单词矩形，返回矩形的各行
    pub fn max_rectangle(words: Vec<String>) -> Vec<String> {
        let mut ans_size = 0;      // 当前找到的最佳矩形面积
        let mut ans = vec![];      // 当前找到的最佳矩形行集合
        let mut max_word_len = 0;  // 输入单词中的最大长度
        let mut trie = Trie::new(); // 全局 Trie，用于前缀匹配

        // 构建 Trie 并统计最大单词长度
        for word in words.iter() {
            trie.insert(word);
            if word.len() > max_word_len { max_word_len = word.len(); }
        }

        // 深度优先搜索构造矩形
        // wl: 当前已选的行（单词引用）
        // tl: 当前每一列在 Trie 中对应的节点
        // words: 所有输入单词（用于候选下一行）
        // max_word_len: 单词最大长度，用于剪枝
        // ans, ans_size: 记录最优解
        fn dfs<'a>(wl: &mut Vec<&'a str>, tl: &Vec<&Trie>, words: &'a Vec<String>,
                   max_word_len: usize, ans: &mut Vec<String>, ans_size: &mut usize) {
            let l = wl.len();                    // 当前矩形的高度
            let last = wl[l-1];                  // 最近添加的一行（新行）
            let mut complete = true;             // 所有列是否都已形成完整单词
            let mut new_tl = vec![];              // 下一行对应的列节点

            // 剪枝：即使剩余行都用最长单词填充，面积也无法超越当前最优
            if last.len() * max_word_len <= *ans_size {
                return
            }

            // 检查新添加的行是否能与之前的所有行构成合法的列单词前缀
            for i in 0..last.len() {
                let b = last.as_bytes()[i];       // 新行在第 i 列的字符
                let t = tl[i].find(b);             // 第 i 列在当前 Trie 节点下继续匹配
                if t.is_none() { return }         // 匹配失败，此路径无效
                if &t.unwrap().word == "" { complete = false }  // 该列尚未形成完整单词
                new_tl.push(t.unwrap());           // 更新该列的 Trie 节点
            }

            // 如果所有列都形成了完整单词，且面积更大，则更新最优解
            if complete && last.len() * l > *ans_size {
                *ans = wl.iter().map(|&x| x.into()).collect::<Vec<_>>();
                *ans_size = last.len() * l;
            }

            // 尝试添加下一行：遍历所有与当前行等长的单词
            for word in words.iter() {
                if word.len() != last.len() { continue }
                wl.push(word);                    // 添加为下一行
                dfs(wl, &new_tl, words, max_word_len, ans, ans_size);
                wl.pop();                         // 回溯
            }
        }

        // 依次以每个单词作为第一行开始搜索
        for word in words.iter() {
            // 初始时每列的 Trie 节点都指向根节点
            dfs(&mut vec![word], &vec![&trie; word.len()], &words,
                max_word_len, &mut ans, &mut ans_size)
        }
        ans  // 返回最优矩形的行集合
    }
}
```
