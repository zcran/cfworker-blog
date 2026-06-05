---
title: "leetcode-字典树23"
date: 2026-06-02T10:23:07+08:00
tags: ["leetcode", "字典树"]
draft: false
---


## 加密解密字符串

给你一个字符数组 keys ，由若干 互不相同 的字符组成。还有一个字符串数组 values ，内含若干长度为 2 的字符串。另给你一个字符串数组 dictionary ，包含解密后所有允许的原字符串。请你设计并实现一个支持加密及解密下标从 0 开始字符串的数据结构。

字符串 加密 按下述步骤进行：

1.对字符串中的每个字符 c ，先从 keys 中找出满足 keys[i] == c 的下标 i 。
2.在字符串中，用 values[i] 替换字符 c 。

请注意，如果 keys 中不存在字符串中的字符，则无法执行加密过程，返回空字符串 ""。

字符串 解密 按下述步骤进行：

1.将字符串每相邻 2 个字符划分为一个子字符串，对于每个子字符串 s ，找出满足 values[i] == s 的一个下标 i 。如果存在多个有效的 i ，从中选择 任意 一个。这意味着一个字符串解密可能得到多个解密字符串。
2.在字符串中，用 keys[i] 替换 s 。

实现 Encrypter 类：

· Encrypter(char[] keys, String[] values, String[] dictionary) 用 keys、values 和 dictionary 初始化 Encrypter 类。
· String encrypt(String word1) 按上述加密过程完成对 word1 的加密，并返回加密后的字符串。
· int decrypt(String word2) 统计并返回可以由 word2 解密得到且出现在 dictionary 中的字符串数目。


```
// 定义加密器结构体
pub struct Encrypter {
    values: Vec<String>,                     // 存储每个字母对应的加密字符串，索引0-25对应a-z
    map: std::collections::HashMap<String, i32>, // 存储加密结果到原始单词数量的映射
}

/**
 * `&self` means the method takes an immutable reference.
 * If you need a mutable reference, change it to `&mut self` instead.
 */
impl Encrypter {
    // 内部加密函数：将单词转换为加密字符串
    // 参数：
    //   values: 字母到加密字符串的映射表
    //   word: 待加密的单词
    // 返回值：加密后的字符串，如果加密后长度不符合预期则返回空字符串
    fn encrypt_inner(values: &Vec<String>, word: String) -> String {
        let word = word.as_bytes();           // 将单词转为字节数组
        let n = word.len();                   // 原始单词长度

        // 使用 fold 迭代器构建加密结果
        let res = word.iter().fold("".to_string(), |mut acc, c| {
            // 将每个字母替换为对应的加密字符串并追加到结果中
            acc.push_str(values[(c - b'a') as usize].as_str());
            acc
        });

        // 验证加密结果：每个字母应该加密为2个字符，所以结果长度应为 n*2
        if res.as_bytes().len() != n * 2 {
            "".to_string()  // 长度不符返回空字符串（无效加密）
        } else {
            res             // 返回有效的加密结果
        }
    }

    // 构造函数：创建新的加密器实例
    // 参数：
    //   keys: 字母列表（需要加密的字符）
    //   values: 与 keys 对应的加密字符串（每个字母加密成2个字符）
    //   dictionary: 词典，包含所有需要支持的原始单词
    pub fn new(keys: Vec<char>, values: Vec<String>, dictionary: Vec<String>) -> Self {
        // 初始化大小为26的数组，存储每个字母对应的加密字符串
        // 默认值为空字符串
        let mut v = vec!["".to_string(); 26];

        // 将 keys 和 values 配对，填充到对应的字母位置
        for (k, val) in keys.into_iter().zip(values) {
            // 将字符转换为索引：'a'->0, 'b'->1, ..., 'z'->25
            v[(k as u8 - b'a') as usize] = val;
        }

        // 创建哈希表，存储加密结果到原始单词数量的映射
        let mut m = std::collections::HashMap::<String, i32>::new();

        // 遍历词典中的每个单词
        for word in dictionary {
            // 加密单词
            let em = Self::encrypt_inner(&v, word);
            // 如果加密结果有效（非空）
            if !em.is_empty() {
                // 统计该加密结果出现的次数
                // or_default() 如果键不存在则插入默认值0
                *m.entry(em).or_default() += 1;
            }
        }

        // 返回 Encrypter 实例
        Self { values: v, map: m }
    }

    // 公开加密方法：加密单个单词
    // 参数：
    //   word1: 待加密的单词
    // 返回值：加密后的字符串
    pub fn encrypt(&self, word1: String) -> String {
        // 调用内部加密函数，使用存储的 values 映射表
        Self::encrypt_inner(&self.values, word1)
    }

    // 公开解密方法：统计加密字符串对应的原始单词数量
    // 参数：
    //   word2: 加密后的字符串
    // 返回值：原始单词的数量（在词典中出现的次数）
    pub fn decrypt(&self, word2: String) -> i32 {
        // 从哈希表中查找加密结果对应的计数
        // cloned() 将 Option<&i32> 转换为 Option<i32>
        // unwrap_or(0) 如果不存在则返回0
        self.map.get(&word2).cloned().unwrap_or(0)
    }
}
```
