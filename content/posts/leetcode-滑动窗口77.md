---
title: "leetcode-滑动窗口77"
date: 2026-07-18T11:02:33+08:00
tags: ["leetcode", "滑动窗口"]
draft: false
---


## 统计完全子字符串

给你一个字符串 word 和一个整数 k 。

如果 word 的一个子字符串 s 满足以下条件，我们称它是 完全字符串：

s 中每个字符 恰好 出现 k 次。

相邻字符在字母表中的顺序 至多 相差 2 。也就是说，s 中两个相邻字符 c1 和 c2 ，它们在字母表中的位置相差 至多 为 2 。

请你返回 word 中 完全 子字符串的数目。

子字符串 指的是一个字符串中一段连续 非空 的字符序列。


```
impl Solution {
    pub fn count_complete_substrings(word: String, k: i32) -> i32 {
        let bytes = word.as_bytes();
        let n = bytes.len();
        let k = k as usize;
        let mut ans = 0;
        let mut start = 0;

        // 按相邻字符差 ≤ 2 分组
        while start < n {
            let mut end = start + 1;
            while end < n && (bytes[end] as i32 - bytes[end - 1] as i32).abs() <= 2 {
                end += 1;
            }

            // 对每个分组 [start, end)，检查所有可能的 m (1..=26)
            ans += Self::count_in_segment(&bytes[start..end], k);
            start = end;
        }

        ans as i32
    }

    /// 统计一个连续段内，长度为 m*k 且每个字符出现 k 次的子串数量
    fn count_in_segment(seg: &[u8], k: usize) -> i32 {
        let mut total = 0;
        let seg_len = seg.len();

        // m 表示不同字符的数量，范围 1..=26
        for m in 1..=26 {
            let window_size = m * k;
            if window_size > seg_len {
                break;
            }

            let mut freq = [0; 26];

            // 初始化第一个窗口
            for &ch in &seg[..window_size] {
                freq[(ch - b'a') as usize] += 1;
            }

            // 检查第一个窗口
            if freq.iter().all(|&c| c == 0 || c == k as i32) {
                total += 1;
            }

            // 滑动窗口
            for right in window_size..seg_len {
                // 移除左边界字符
                let left_ch = seg[right - window_size];
                freq[(left_ch - b'a') as usize] -= 1;

                // 加入右边界字符
                let right_ch = seg[right];
                freq[(right_ch - b'a') as usize] += 1;

                // 检查当前窗口是否满足条件
                if freq.iter().all(|&c| c == 0 || c == k as i32) {
                    total += 1;
                }
            }
        }

        total
    }
}
```
