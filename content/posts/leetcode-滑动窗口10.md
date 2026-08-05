---
title: "leetcode-滑动窗口10"
date: 2026-07-18T11:02:29+08:00
tags: ["leetcode", "滑动窗口"]
draft: false
---


## 字符串的排列

给你两个字符串 s1 和 s2 ，写一个函数来判断 s2 是否包含 s1 的 排列。如果是，返回 true ；否则，返回 false 。

换句话说，s1 的排列之一是 s2 的 子串 。


```
impl Solution {
    pub fn check_inclusion(s1: String, s2: String) -> bool {
        let (s1, s2) = (s1.as_bytes(), s2.as_bytes());
        let (n, m) = (s1.len(), s2.len());
        if n > m {
            return false;
        }

        // 统计 s1 的字符频率，并记录差异数
        let mut diff = [0; 26];
        for &ch in s1 {
            diff[(ch - b'a') as usize] += 1;
        }

        // 初始化第一个窗口：s2[0..n) 减去 s1 的频率
        for i in 0..n {
            diff[(s2[i] - b'a') as usize] -= 1;
        }

        // 检查差异数是否为 0
        let mut zero_count = diff.iter().filter(|&&x| x == 0).count();
        if zero_count == 26 {
            return true;
        }

        // 滑动窗口
        for i in n..m {
            // 移出左边界
            let out = (s2[i - n] - b'a') as usize;
            if diff[out] == 0 {
                zero_count -= 1;
            }
            diff[out] += 1;
            if diff[out] == 0 {
                zero_count += 1;
            }

            // 移入右边界
            let in_char = (s2[i] - b'a') as usize;
            if diff[in_char] == 0 {
                zero_count -= 1;
            }
            diff[in_char] -= 1;
            if diff[in_char] == 0 {
                zero_count += 1;
            }

            if zero_count == 26 {
                return true;
            }
        }

        false
    }
}
```
