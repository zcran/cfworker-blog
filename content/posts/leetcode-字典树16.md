---
title: "leetcode-字典树16"
date: 2026-06-02T10:23:07+08:00
tags: ["leetcode", "字典树"]
draft: false
---


## 单词的压缩编码

单词数组 words 的 有效编码 由任意助记字符串 s 和下标数组 indices 组成，且满足：

· words.length == indices.length
· 助记字符串 s 以 '#' 字符结尾
· 对于每个下标 indices[i] ，s 的一个从 indices[i] 开始、到下一个 '#' 字符结束（但不包括 '#'）的 子字符串 恰好与 words[i] 相等

给你一个单词数组 words ，返回成功对 words 进行编码的最小助记字符串 s 的长度 。


```
impl Solution {
    pub fn minimum_length_encoding(mut words: Vec<String>) -> i32 {
        // 1. 将单词按长度从小到大排序（unstable 排序，性能更好）
        //    length: 将长度转为 i32 作为排序键
        words.sort_unstable_by_key(|w| w.len() as i32);

        // 2. 创建结果数组，预分配容量避免重复分配
        let mut tab: Vec<String> = Vec::with_capacity(words.len());

        // 3. 从最长的单词开始处理（pop 从末尾取）
        while let Some(w) = words.pop() {
            let mut found = false;

            // 4. 检查当前单词是否是 tab 中某个单词的后缀
            //    如果是后缀，则可以被已存在的单词覆盖，不需要单独编码
            for i in tab.iter() {
                if i.ends_with(&w) {
                    found = true;
                    break;
                }
            }

            // 5. 如果不是任何单词的后缀，则需要加入编码列表
            if !found {
                tab.push(w);
            }
        }

        // 6. 计算总长度：每个单词长度 + 1（井号分隔符）
        tab.into_iter().fold(0, |ans, w| ans + w.len() as i32 + 1)
    }
}
```
