---
title: "leetcode-字典树10"
date: 2026-06-02T10:23:07+08:00
tags: ["leetcode", "字典树"]
draft: false
---


## 实现一个魔法字典

设计一个使用单词列表进行初始化的数据结构，单词列表中的单词 互不相同 。 如果给出一个单词，请判定能否只将这个单词中一个字母换成另一个字母，使得所形成的新单词存在于你构建的字典中。

实现 MagicDictionary 类：

MagicDictionary() 初始化对象
void buildDict(String[] dictionary) 使用字符串数组 dictionary 设定该数据结构，dictionary 中的字符串互不相同
bool search(String searchWord) 给定一个字符串 searchWord ，判定能否只将字符串中 一个 字母换成另一个字母，使得所形成的新字符串能够与字典中的任一字符串匹配。如果可以，返回 true ；否则，返回 false 。


```
/// 魔幻字典 - 使用暴力匹配实现
///
/// # 功能
/// 构建一个字典，然后查询某个单词是否与字典中某个单词**恰好有一个字符不同**
struct MagicDictionary {
    /// 存储所有词典单词（原始字符串）
    vec: Vec<String>,
}

impl MagicDictionary {
    /// 创建一个新的魔幻字典
    fn new() -> Self {
        Self { vec: vec![] }
    }

    /// 构建字典（批量添加单词）
    fn build_dict(&mut self, dictionary: Vec<String>) {
        self.vec.extend(dictionary);
    }

    /// 搜索是否存在与 `search_word` 恰好有一个字符不同的单词
    ///
    /// # 算法步骤
    /// 1. 过滤出所有与目标单词长度相同的候选词
    /// 2. 逐个比较，统计不同字符的数量
    /// 3. 如果不同字符数恰好为 1，返回 true
    fn search(&self, search_word: String) -> bool {
        let target_len = search_word.len();
        let target_bytes = search_word.as_bytes();

        // 只考虑长度相同的单词（长度不同肯定不匹配）
        for dict_word in self.vec.iter().filter(|w| w.len() == target_len) {
            let dict_bytes = dict_word.as_bytes();
            let mut diff_count = 0;

            // 逐字节比较，统计不同字符的数量
            for i in 0..target_len {
                if dict_bytes[i] != target_bytes[i] {
                    diff_count += 1;
                    // 提前终止优化：已经超过 1 个不同，无需继续比较
                    if diff_count > 1 {
                        break;
                    }
                }
            }

            // 精确匹配：必须恰好有 1 个字符不同
            if diff_count == 1 {
                return true;
            }
        }

        false
    }
}
```
