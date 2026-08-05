---
title: "leetcode-滑动窗口29"
date: 2026-07-18T11:02:30+08:00
tags: ["leetcode", "滑动窗口"]
draft: false
---


## 替换子串得到平衡字符串

有一个只含有 'Q', 'W', 'E', 'R' 四种字符，且长度为 n 的字符串。

假如在该字符串中，这四个字符都恰好出现 n/4 次，那么它就是一个「平衡字符串」。



给你一个这样的字符串 s，请通过「替换一个子串」的方式，使原字符串 s 变成一个「平衡字符串」。

你可以用和「待替换子串」长度相同的 任何 其他字符串来完成替换。

请返回待替换子串的最小可能长度。

如果原字符串自身就是一个平衡字符串，则返回 0。


```
impl Solution {
    pub fn balanced_string(s: String) -> i32 {
        let s = s.as_bytes();
        let n = s.len();
        let target = n / 4;

        // 统计每个字符的总出现次数
        let mut count = [0; 26];
        for &ch in s {
            count[(ch - b'A') as usize] += 1;
        }

        // 辅助函数：获取字符对应的索引
        let idx = |c: u8| (c - b'A') as usize;

        // 如果已经平衡，直接返回0
        if count[idx(b'Q')] == target && count[idx(b'W')] == target
            && count[idx(b'E')] == target && count[idx(b'R')] == target {
            return 0;
        }

        let mut min_len = n;
        let mut left = 0;

        // 滑动窗口：寻找最短子串，使得外部字符都不超过 target
        for right in 0..n {
            count[idx(s[right])] -= 1;

            // 当外部所有字符数量都不超过 target 时，当前窗口可以替换
            while count[idx(b'Q')] <= target && count[idx(b'W')] <= target
                && count[idx(b'E')] <= target && count[idx(b'R')] <= target {
                min_len = min_len.min(right - left + 1);
                // 移动左指针，恢复被移出窗口的字符计数
                count[idx(s[left])] += 1;
                left += 1;
            }
        }

        min_len as i32
    }
}
```
