---
title: "leetcode-计数75"
date: 2026-08-01T10:40:55+08:00
tags: ["leetcode", "计数"]
draft: false
---


## 删除字符使频率相同

你一个下标从 0 开始的字符串 word ，字符串只包含小写英文字母。你需要选择 一个 下标并 删除 下标处的字符，使得 word 中剩余每个字母出现 频率 相同。

如果删除一个字母后，word 中剩余所有字母的出现频率都相同，那么返回 true ，否则返回 false 。

注意：

字母 x 的 频率 是这个字母在字符串中出现的次数。

你 必须 恰好删除一个字母，不能一个字母都不删除。


```
impl Solution {
    pub fn equal_frequency(word: String) -> bool {
        // 统计每个字母出现次数
        let mut count = [0; 26];
        for &b in word.as_bytes() {
            count[(b - b'a') as usize] += 1;
        }

        // 获取所有非零频率
        let mut freqs: Vec<i32> = count.into_iter().filter(|&c| c > 0).collect();

        // 尝试删除每个不同频率对应的一个字符
        // 只需检查不同频率值，而非每个位置
        let mut checked = std::collections::HashSet::new();
        for &freq in &freqs {
            if checked.contains(&freq) {
                continue;
            }
            checked.insert(freq);

            // 尝试将一个频率为 freq 的字母减少 1
            let mut modified = false;
            let mut new_freqs = Vec::new();
            for &c in &freqs {
                if c == freq && !modified {
                    if c - 1 > 0 {
                        new_freqs.push(c - 1);
                    }
                    modified = true;
                } else {
                    new_freqs.push(c);
                }
            }

            // 如果成功修改，检查是否所有频率相同
            if modified && new_freqs.windows(2).all(|w| w[0] == w[1]) {
                return true;
            }
        }

        false
    }
}
```
