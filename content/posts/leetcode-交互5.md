---
title: "leetcode-交互5"
date: 2026-03-31T20:54:46+08:00
tags: ["leetcode", "交互"]
draft: false
---


## 猜猜这个单词

给你一个由 不同 字符串组成的单词列表 words ，其中 words[i] 长度均为 6 。words 中的一个单词将被选作秘密单词 secret 。

另给你一个辅助对象 Master ，你可以调用 Master.guess(word) 来猜单词，其中参数 word 长度为 6 且必须是 words 中的字符串。

Master.guess(word) 将会返回如下结果：

如果 word 不是 words 中的字符串，返回 -1 ，或者
一个整数，表示你所猜测的单词 word 与 秘密单词 secret 的准确匹配（值和位置同时匹配）的数目。
每组测试用例都会包含一个参数 allowedGuesses ，其中 allowedGuesses 是你可以调用 Master.guess(word) 的最大次数。

对于每组测试用例，在不超过允许猜测的次数的前提下，你应该调用 Master.guess 来猜出秘密单词。最终，你将会得到以下结果：

如果你调用 Master.guess 的次数大于 allowedGuesses 所限定的次数或者你没有用 Master.guess 猜到秘密单词，则得到 "Either you took too many guesses, or you did not find the secret word." 。
如果你调用 Master.guess 猜到秘密单词，且调用 Master.guess 的次数小于或等于 allowedGuesses ，则得到 "You guessed the secret word correctly." 。
生成的测试用例保证你可以利用某种合理的策略（而不是暴力）猜到秘密单词。

```
impl Solution {
    pub fn find_secret_word(words: Vec<String>, master: &Master) {
        let n = words.len();
        let similarity = (0..n).map(|i| (0..n).map(|j|
            words[i].chars().zip(words[j].chars()).filter(|(c1, c2)| c1 == c2).count()
        ).collect::<Vec<_>>()).collect::<Vec<_>>();
        let mut q: Vec<_> = (0..n).collect();
        loop {
            // 选择下一个要猜的词
            let idx = *q.iter().min_by_key(|&&i| {
                // 计算如果猜第 i 个词，最坏情况下的分组大小
                let mut cnt = [0; 7];
                q.iter().for_each(|&j| cnt[similarity[i][j]] += 1);
                *cnt.iter().max().unwrap()    // 返回最大分组的大小
            }).unwrap();

            let sim = master.guess(words[idx].clone()) as usize;
            println!("{}, {}", sim, words[idx]);
            if sim == 6 { return; }
            q = q.into_iter().filter(|&j| similarity[idx][j] == sim).collect();
        }
    }
}

```


1. 预处理相似度矩阵

计算所有词对之间的相似度（相同位置相同字符数）
similarity[i][j] 表示 words[i] 和 words[j] 的匹配字符数
结果是一个 n x n 的矩阵，预计算避免重复计算


2. 初始化候选词索引队列

q 存储当前仍可能是答案的单词索引
初始时所有单词都在候选集中


3. 选择策略：选择最小化最坏情况分组大小的词

min_by_key：找到使 max_group_size 最小的词
cnt[similarity[i][j]]：按相似度统计分组
*cnt.iter().max().unwrap()：当前词 i 最坏情况下的候选数量
这是极小化极大算法，目标是尽快缩小候选集。

4. 猜词并获取结果

master.guess()：调用 API 猜词，返回匹配字符数（0-6）
sim == 6：猜中（6 个字符全匹配），直接返回

5. 更新候选集

只保留与当前猜的词相似度等于 sim 的词
因为真实答案必然与当前猜的词有 sim 个匹配字符
