---
title: "leetcode-计数90"
date: 2026-08-01T10:40:55+08:00
tags: ["leetcode", "计数"]
draft: false
---


## 回文字符串的最大数量

给你一个下标从 0 开始的字符串数组 words ，数组的长度为 n ，且包含下标从 0 开始的若干字符串。

你可以执行以下操作 任意 次数（包括零次）：

选择整数i、j、x和y，满足0 <= i, j < n，0 <= x < words[i].length，0 <= y < words[j].length，交换 字符 words[i][x] 和 words[j][y] 。
返回一个整数，表示在执行一些操作后，words 中可以包含的回文串的 最大 数量。

注意：在操作过程中，i 和 j 可以相等。


```
impl Solution {
    pub fn max_palindromes_after_operations(words: Vec<String>) -> i32 {
        // 统计所有字符出现次数
        let mut cnt = [0; 26];
        let mut sizes = Vec::with_capacity(words.len());
        for word in &words {
            sizes.push(word.len());
            for b in word.bytes() {
                cnt[(b - b'a') as usize] += 1;
            }
        }

        // 计算总配对数和单字符数
        let mut pairs = 0;
        let mut singles = 0;
        for c in cnt {
            pairs += c / 2;
            singles += c % 2;
        }

        // 贪心：优先构造短的回文串（消耗更少资源）
        sizes.sort_unstable();

        let mut res = 0;
        for len in sizes {
            let need_pairs = len / 2;
            let need_single = len % 2;

            // 奇数长度需要1个单字符放中间
            if need_single > 0 {
                if singles > 0 {
                    singles -= 1;           // 直接用现有的单字符
                } else if pairs > 0 {
                    pairs -= 1;             // 拆一对，产生2个单字符，用1个剩1个
                    singles += 1;
                } else {
                    break;                  // 资源不足
                }
            }

            // 需要足够的配对
            if pairs >= need_pairs {
                pairs -= need_pairs;
                res += 1;
            } else {
                break;
            }
        }

        res
    }
}
```
