---
title: "leetcode-回溯68"
date: 2026-07-04T10:22:04+08:00
tags: ["leetcode", "回溯"]
draft: false
---


## 单词方块 II

给你一个字符串数组 words，包含一组 互不相同 且由小写英文字母组成的四字母字符串。

单词方块 由 4 个 互不相同 的单词组成：top, left, right 和 bottom，它们按如下方式排列：

· top 形成 顶部行 。
· bottom 形成 底部行 。
· left 形成 左侧列（从上到下）。
· right 形成 右侧列（从上到下）。

它必须满足以下条件：

· top[0] == left[0], top[3] == right[0]
· bottom[0] == left[3], bottom[3] == right[3]

返回所有满足题目要求的 不同 单词方块，按 4 元组 (top, left, right, bottom)的 字典序升序 排序。


```
impl Solution {
    pub fn word_squares(words: Vec<String>) -> Vec<Vec<String>> {
        // 如果单词数量少于 4，无法组成单词方块
        if words.len() < 4 {
            return Vec::new();
        }

        let mut result = Vec::new();

        // 四重循环枚举 top, left, right, bottom 的所有组合
        for i in 0..words.len() {
            let top = &words[i];
            // 只考虑长度为 4 的单词
            if top.len() != 4 {
                continue;
            }
            let top_chars: Vec<char> = top.chars().collect();

            for j in 0..words.len() {
                if j == i {
                    continue;
                }
                let left = &words[j];
                if left.len() != 4 {
                    continue;
                }
                let left_chars: Vec<char> = left.chars().collect();

                // 条件 1: top[0] == left[0]
                if top_chars[0] != left_chars[0] {
                    continue;
                }

                for k in 0..words.len() {
                    if k == i || k == j {
                        continue;
                    }
                    let right = &words[k];
                    if right.len() != 4 {
                        continue;
                    }
                    let right_chars: Vec<char> = right.chars().collect();

                    // 条件 2: top[3] == right[0]
                    if top_chars[3] != right_chars[0] {
                        continue;
                    }

                    for l in 0..words.len() {
                        if l == i || l == j || l == k {
                            continue;
                        }
                        let bottom = &words[l];
                        if bottom.len() != 4 {
                            continue;
                        }
                        let bottom_chars: Vec<char> = bottom.chars().collect();

                        // 条件 3: bottom[0] == left[3]
                        // 条件 4: bottom[3] == right[3]
                        if bottom_chars[0] == left_chars[3]
                            && bottom_chars[3] == right_chars[3]
                        {
                            // 所有条件满足，且四个词互不相同（已通过下标保证）
                            result.push(vec![
                                top.clone(),
                                left.clone(),
                                right.clone(),
                                bottom.clone(),
                            ]);
                        }
                    }
                }
            }
        }

        // 按字典序排序并去重（虽然下标组合不会重复，但以防万一）
        result.sort();
        result.dedup();
        result
    }
}
```
