---
title: "leetcode-字典树14"
date: 2026-06-02T10:23:07+08:00
tags: ["leetcode", "字典树"]
draft: false
---


## 前缀和后缀搜索

设计一个包含一些单词的特殊词典，并能够通过前缀和后缀来检索单词。

实现 WordFilter 类：

WordFilter(string[] words) 使用词典中的单词 words 初始化对象。
f(string pref, string suff) 返回词典中具有前缀 pref 和后缀 suff 的单词的下标。如果存在不止一个满足要求的下标，返回其中 最大的下标 。如果不存在这样的单词，返回 -1 。

```
/// 前缀树节点结构
///
/// 用于存储单词并支持高效的前缀/后缀查询
/// 使用 27 个分支（26 个小写字母 + 1 个特殊分隔符 '{'）
#[derive(Default)]  // 自动实现 Default trait，创建默认值
struct Trie {
    /// 当前节点对应的最大单词索引
    /// 存储经过此节点的所有单词中的最大索引值
    index: i32,

    /// 27 个子节点（26 个字母 a-z + 分隔符 '{'）
    /// '{' 的 ASCII 码是 123，比 'z' (122) 大 1
    /// 使用 Option 表示节点可能存在，Box 将节点分配在堆上
    next: [Option<Box<Trie>>; 27],
}

/// 单词过滤器结构
///
/// 支持通过前缀和后缀同时过滤单词，返回匹配单词中的最大索引
struct WordFilter {
    trie: Trie,  // 前缀树根节点
}

impl WordFilter {
    /// 创建新的 WordFilter 实例
    ///
    /// # 参数
    /// - `words`: 单词列表，每个单词有对应的索引（位置）
    ///
    /// # 算法思路
    /// 为了同时支持前缀和后缀查询，使用技巧：
    /// 1. 将单词拼接成 "suffix{word" 的形式
    /// 2. 插入所有后缀组合
    /// 3. 每个节点存储经过它的最大单词索引
    ///
    /// # 示例
    /// 对于单词 "apple" (索引 i)：
    /// 会插入以下模式：
    /// - "apple{apple"   (从开头开始)
    /// - "pple{apple"    (从第1个字符开始)
    /// - "ple{apple"     (从第2个字符开始)
    /// - "le{apple"      (从第3个字符开始)
    /// - "e{apple"       (从第4个字符开始)
    fn new(words: Vec<String>) -> Self {
        // 创建根节点
        let mut trie = Trie::default();

        // 遍历每个单词，enumerate() 获取索引
        for (i, word) in words.iter().enumerate() {
            // 构造模式字符串：原单词 + "{" + 原单词
            // 使用 "{" 作为分隔符，因为它的 ASCII 码比 'z' 大 1
            // 这样可以保持字典序，同时作为特殊标记
            let s = String::new() + &word + "{" + &word;

            // 对于每个可能的后缀起始位置
            // 例如：word = "apple"
            // j = 0: 插入 "apple{apple"
            // j = 1: 插入 "pple{apple"
            // j = 2: 插入 "ple{apple"
            // j = 3: 插入 "le{apple"
            // j = 4: 插入 "e{apple"
            for j in 0..word.len() {
                // 从根节点开始
                let mut curr = &mut trie;

                // 遍历从 j 开始的子串
                // &s.as_bytes()[j..] 获取从 j 到末尾的字节切片
                for &b in &s.as_bytes()[j..] {
                    // 计算索引：字符 - 'a' 得到 0-25
                    // 对于 '{'，它的 ASCII 是 123，减去 'a' (97) 得到 26
                    let idx = (b - b'a') as usize;

                    // 获取或创建子节点
                    // get_or_insert_with: 如果节点不存在，用 Default::default() 创建
                    // Default::default() 会创建新的 Trie 实例（所有字段为默认值）
                    curr = curr.next[idx]
                        .get_or_insert_with(Default::default);

                    // 更新当前节点的索引为当前单词的索引（取最大值）
                    // 由于遍历是按顺序的，后面的单词索引更大，所以直接赋值即可
                    curr.index = i as i32;
                }
            }
        }

        // 返回包含构建好的前缀树的 WordFilter
        Self { trie }
    }

    /// 查找同时匹配给定前缀和后缀的单词的最大索引
    ///
    /// # 参数
    /// - `prefix`: 前缀字符串
    /// - `suffix`: 后缀字符串
    ///
    /// # 返回
    /// 匹配单词的最大索引，如果没有匹配则返回 -1
    ///
    /// # 算法
    /// 构造查询字符串：suffix + "{" + prefix
    /// 在前缀树中查找这个字符串，返回节点存储的索引
    ///
    /// # 示例
    /// ```
    /// let filter = WordFilter::new(vec!["apple".to_string()]);
    /// assert_eq!(filter.f("app".to_string(), "e".to_string()), 0);
    /// ```
    fn f(&self, prefix: String, suffix: String) -> i32 {
        // 从根节点开始
        let mut curr = &self.trie;

        // 构造查询字符串：后缀 + "{" + 前缀
        // 这与插入时的模式相对应
        let s = String::new() + &suffix + "{" + &prefix;

        // 遍历查询字符串的每个字符
        for &b in s.as_bytes() {
            // 计算子节点索引
            let idx = (b - b'a') as usize;

            // 尝试获取子节点
            if let Some(n) = &curr.next[idx] {
                // 存在，继续向下遍历
                curr = n.as_ref();
            } else {
                // 路径不存在，没有匹配的单词
                return -1;
            }
        }

        // 返回最后一个节点存储的索引（经过该节点的最大单词索引）
        curr.index
    }
}
```
