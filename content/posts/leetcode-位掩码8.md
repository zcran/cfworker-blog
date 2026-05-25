---
title: "leetcode-位掩码8"
date: 2026-05-18T10:27:58+08:00
tags: ["leetcode", "位掩码"]
draft: false
---


## 得分最高的单词集合

你将会得到一份单词表 words，一个字母表 letters （可能会有重复字母），以及每个字母对应的得分情况表 score。

请你帮忙计算玩家在单词拼写游戏中所能获得的「最高得分」：能够由 letters 里的字母拼写出的 任意 属于 words 单词子集中，分数最高的单词集合的得分。

单词拼写游戏的规则概述如下：

玩家需要用字母表 letters 里的字母来拼写单词表 words 中的单词。
可以只使用字母表 letters 中的部分字母，但是每个字母最多被使用一次。
单词表 words 中每个单词只能计分（使用）一次。
根据字母得分情况表score，字母 'a', 'b', 'c', ... , 'z' 对应的得分分别为 score[0], score[1], ..., score[25]。
本场游戏的「得分」是指：玩家所拼写出的单词集合里包含的所有字母的得分之和。

```
impl Solution {
    pub fn max_score_words(words: Vec<String>, letters: Vec<char>, score: Vec<i32>) -> i32 {
        let n = words.len();
        // 可用字母计数（u8 足够，因为 letters 最多 100 个）
        let mut avail = vec![0u8; 26];
        for c in letters {
            avail[(c as u8 - b'a') as usize] += 1;
        }

        // 预处理每个单词的字母计数和分数
        let mut word_cnt = vec![[0u8; 26]; n];
        let mut word_score = vec![0; n];
        for (i, w) in words.iter().enumerate() {
            let mut cnt = [0u8; 26];
            let mut sc = 0;
            for c in w.chars() {
                let idx = (c as u8 - b'a') as usize;
                cnt[idx] += 1;
                sc += score[idx];
            }
            word_cnt[i] = cnt;
            word_score[i] = sc;
        }

        let total_masks = 1 << n;
        // dp_letters[mask][c] = 使用 mask 单词集合时字母 c 的总用量
        let mut dp_letters = vec![[0u8; 26]; total_masks];
        let mut dp_score = vec![0; total_masks];

        // 从小到大遍历所有 mask（保证子状态已计算）
        for mask in 1..total_masks {
            // 枚举最后加入的单词 i
            for i in 0..n {
                if (mask >> i) & 1 == 0 { continue; }
                let prev = mask ^ (1 << i);
                // 检查加入单词 i 后是否超过可用字母
                let mut ok = true;
                for c in 0..26 {
                    if dp_letters[prev][c] + word_cnt[i][c] > avail[c] {
                        ok = false;
                        break;
                    }
                }
                if !ok { continue; }
                // 计算新分数
                let new_score = dp_score[prev] + word_score[i];
                if new_score > dp_score[mask] {
                    dp_score[mask] = new_score;
                    // 更新字母用量（直接复制并加上）
                    dp_letters[mask] = dp_letters[prev];
                    for c in 0..26 {
                        dp_letters[mask][c] += word_cnt[i][c];
                    }
                }
            }
        }

        *dp_score.iter().max().unwrap()
    }
}
```
