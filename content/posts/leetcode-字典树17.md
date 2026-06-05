---
title: "leetcode-字典树17"
date: 2026-06-02T10:23:07+08:00
tags: ["leetcode", "字典树"]
draft: false
---


## 猜字谜

外国友人仿照中国字谜设计了一个英文版猜字谜小游戏，请你来猜猜看吧。

字谜的迷面 puzzle 按字符串形式给出，如果一个单词 word 符合下面两个条件，那么它就可以算作谜底：

· 单词 word 中包含谜面 puzzle 的第一个字母。
· 单词 word 中的每一个字母都可以在谜面 puzzle 中找到。
例如，如果字谜的谜面是 "abcdefg"，那么可以作为谜底的单词有 "faced", "cabbage", 和 "baggage"；而 "beefed"（不含字母 "a"）以及 "based"（其中的 "s" 没有出现在谜面中）都不能作为谜底。

返回一个答案数组 answer，数组中的每个元素 answer[i] 是在给出的单词列表 words 中可以作为字谜迷面 puzzles[i] 所对应的谜底的单词数目。


```
impl Solution {
    pub fn find_num_of_valid_words(words: Vec<String>, puzzles: Vec<String>) -> Vec<i32> {
        let mut result = Vec::new();

        // 1. 创建位掩码数组，大小为 2^26（因为只有 a-z 26个小写字母）
        //    但实际上这里创建了 2^27-1 的大小，有点浪费空间
        let mut words_set = vec![0; (2 as usize).pow(27)-1];

        // 2. 预处理每个单词，转换为位掩码并统计出现次数
        for word in words {
            let mut posts = 0;  // 位掩码，记录单词中包含哪些字母
            for character in word.chars() {
                // 将字母转换为 bit 位置，例如 'a' -> 0, 'b' -> 1
                posts |= (1 << (character as i32 - 'a' as i32));
            }
            // 统计该位掩码对应的单词数量
            words_set[posts] += 1;
        }

        // 3. 处理每个谜面
        for puzzle in puzzles {
            let mut count = 0;

            // 谜面的位掩码（不包含首字母）
            let mut posts = 0;
            let mut characters = puzzle.chars();
            let head = characters.nth(0).unwrap();  // 谜面的首字母

            // 计算除首字母外的字母位掩码
            for character in characters {
                posts |= (1 << (character as i32 - 'a' as i32));
            }

            // 4. 枚举 posts 的所有子集（sub 是首字母之外的可能组合）
            let mut sub = posts;
            loop {
                // 关键：每个有效单词必须包含 puzzle 的首字母
                // 所以实际位掩码 = sub + 首字母位
                count += words_set[sub + (1 << (head as i32 - 'a' as i32))];

                // 生成下一个子集（经典的子集枚举技巧）
                sub = (sub - 1) & posts;

                // 当 sub 回到 posts 时表示枚举完毕
                if sub == posts { break; }
            }
            result.push(count);
        }

        result
    }
}
```
