---
title: "leetcode-模拟5"
date: 2026-08-08T11:31:08+08:00
tags: ["leetcode", "模拟"]
draft: false
---


## 文本左右对齐

给定一个单词数组 words 和一个长度 maxWidth ，重新排版单词，使其成为每行恰好有 maxWidth 个字符，且左右两端对齐的文本。

你应该使用 “贪心算法” 来放置给定的单词；也就是说，尽可能多地往每行中放置单词。必要时可用空格 ' ' 填充，使得每行恰好有 maxWidth 个字符。

要求尽可能均匀分配单词间的空格数量。如果某一行单词间的空格不能均匀分配，则左侧放置的空格数要多于右侧的空格数。

文本的最后一行应为左对齐，且单词之间不插入额外的空格。

注意:

单词是指由非空格字符组成的字符序列。
每个单词的长度大于 0，小于等于 maxWidth。
输入单词数组 words 至少包含一个单词。


```
impl Solution {
    pub fn full_justify(words: Vec<String>, max_width: i32) -> Vec<String> {
        let max_width = max_width as usize;
        let mut ans = Vec::new();
        let mut i = 0;

        while i < words.len() {
            let start = i;
            let mut line_len = words[i].len();
            i += 1;

            // 贪心：尽可能多地放单词（单词间至少1个空格）
            while i < words.len() && line_len + 1 + words[i].len() <= max_width {
                line_len += words[i].len() + 1;
                i += 1;
            }

            let gaps = i - start - 1;
            let extra = max_width - line_len;

            // 单行或最后一行：左对齐
            if gaps == 0 || i == words.len() {
                let mut row = words[start..i].join(" ");
                row.push_str(&" ".repeat(extra));
                ans.push(row);
                continue;
            }

            // 普通行：均匀分配空格，左侧空隙更多
            let base = extra / gaps + 1;      // 每个空隙的基础空格数（含原有的1个）
            let more = extra % gaps;          // 前 more 个空隙多1个空格
            let mut row = String::with_capacity(max_width);

            for j in start..i {
                row.push_str(&words[j]);
                if j < i - 1 {
                    let spaces = if j - start < more { base + 1 } else { base };
                    row.push_str(&" ".repeat(spaces));
                }
            }
            ans.push(row);
        }

        ans
    }
}
```
