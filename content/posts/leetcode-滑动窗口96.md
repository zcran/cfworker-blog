---
title: "leetcode-滑动窗口96"
date: 2026-07-18T11:02:34+08:00
tags: ["leetcode", "滑动窗口"]
draft: false
---


## 统计重新排列后包含另一个字符串的子字符串数目 I

给你两个字符串 word1 和 word2 。

如果一个字符串 x 重新排列后，word2 是重排字符串的 前缀 ，那么我们称字符串 x 是 合法的 。

请你返回 word1 中 合法 子字符串 的数目。


```
impl Solution {
    pub fn valid_substring_count(word1: String, word2: String) -> i64 {
        // 1. 统计 word2 中每个字符的需求量
        let mut required = vec![0; 26];
        for ch in word2.chars() {
            required[(ch as u8 - b'a') as usize] += 1;
        }

        let word1_bytes = word1.as_bytes();
        let n = word1_bytes.len();

        // 2. 构建前缀频率数组：pre_count[i][c] 表示 word1[0..i) 中字符 c 的出现次数
        let mut prefix_freq = vec![vec![0; 26]; n + 1];
        for i in 1..=n {
            prefix_freq[i] = prefix_freq[i - 1].clone();
            let ch_idx = (word1_bytes[i - 1] - b'a') as usize;
            prefix_freq[i][ch_idx] += 1;
        }

        // 3. 二分查找函数：找到最小的 m，使得 word1[border..m] 包含 word2 的所有字符
        let find_min_right = |mut left: usize, mut right: usize| -> usize {
            let border = left - 1; // 左边界的前一个位置
            while left < right {
                let mid = (left + right) / 2;
                let mut is_valid = true;

                // 检查区间 [border, mid) 是否包含所有 required 字符
                for c in 0..26 {
                    if prefix_freq[mid][c] - prefix_freq[border][c] < required[c] {
                        is_valid = false;
                        break;
                    }
                }

                if is_valid {
                    right = mid; // 尝试更小的右边界
                } else {
                    left = mid + 1; // 扩大右边界
                }
            }
            left
        };

        // 4. 枚举每个左边界，计算合法子串数量
        let mut total_count = 0;
        for left in 1..=n {
            let min_right = find_min_right(left, n + 1);
            // 所有以 [left, min_right) 为起点的子串都合法
            // 右端点可以从 min_right 到 n，共 (n + 1 - min_right) 个
            total_count += (n + 1 - min_right) as i64;
        }

        total_count
    }
}
```
