---
title: "leetcode-模拟126"
date: 2026-08-08T11:31:16+08:00
tags: ["leetcode", "模拟"]
draft: false
---


## 为视频标题生成标签

给你一个字符串 caption，表示一个视频的标题。

需要按照以下步骤 按顺序 生成一个视频的 有效标签 ：

1. 将 所有单词 组合为单个 驼峰命名字符串 ，并在前面加上 '#'。驼峰命名字符串 指的是除第一个单词外，其余单词的首字母大写，且每个单词的首字母之后的字符必须是小写。

2. 移除 所有不是英文字母的字符，但 保留 第一个字符 '#'。

3. 将结果 截断 为最多 100 个字符。

对 caption 执行上述操作后，返回生成的 标签 。


```
impl Solution {
    /// 根据视频标题生成有效标签
    ///
    /// 生成规则（按顺序）：
    /// 1. 将所有单词组合为驼峰命名字符串，前面加 '#'：
    ///    - 第一个单词全小写
    ///    - 后续单词首字母大写，其余小写
    /// 2. 移除所有非英文字母字符，但保留开头的 '#'
    /// 3. 截断为最多 100 个字符
    ///
    /// # 参数
    /// - `caption`: 视频标题字符串
    ///
    /// # 返回值
    /// - 生成的标签字符串
    pub fn generate_tag(caption: String) -> String {
        let mut tag = String::with_capacity(101);
        tag.push('#');

        // 按空白字符分割标题为单词
        let words: Vec<&str> = caption.split_whitespace().collect();
        let is_first_word = |idx: usize| idx == 0;

        for (idx, word) in words.iter().enumerate() {
            // 步骤 1：处理每个单词的字母大小写
            let processed: String = word
                .chars()
                .filter(|c| c.is_ascii_alphabetic()) // 步骤 2：过滤非字母
                .enumerate()
                .map(|(i, c)| {
                    let lower = c.to_ascii_lowercase();
                    if !is_first_word(idx) && i == 0 {
                        lower.to_ascii_uppercase() // 非首单词的首字母大写
                    } else {
                        lower // 其余字母小写
                    }
                })
                .collect();

            tag.push_str(&processed);

            // 步骤 3：提前截断，避免无意义处理
            if tag.len() >= 100 {
                tag.truncate(100);
                break;
            }
        }

        tag
    }
}
```
