---
title: "leetcode-枚举86"
date: 2026-07-09T10:05:03+08:00
tags: ["leetcode", "枚举"]
draft: false
---


## 最长的平衡子串 I

给你一个由小写英文字母组成的字符串 s。

如果一个 子串 中所有 不同 字符出现的次数都 相同 ，则称该子串为 平衡 子串。

请返回 s 的 最长平衡子串 的 长度 。

子串 是字符串中连续的、非空 的字符序列。


```
impl Solution {
    pub fn longest_balanced(s: String) -> i32 {
        let bytes = s.as_bytes();
        let n = bytes.len();
        let mut ans = 0;

        // 枚举所有可能的平衡子串长度（只需考虑因子数量 ≤ 26 的长度）
        // 优化：平衡子串中不同字符数 d 必须满足 len % d == 0
        for len in 1..=n {
            // 不同字符数 d 必须是 len 的因子且 d ≤ 26
            for d in 1..=26 {
                if len % d != 0 {
                    continue;
                }
                let target = (len / d) as i32; // 每个不同字符应出现的次数

                // 滑动窗口检查长度为 len 的子串是否平衡
                let mut cnt = [0; 26];
                let mut distinct = 0;

                // 初始化第一个窗口
                for i in 0..len {
                    let idx = (bytes[i] - b'a') as usize;
                    if cnt[idx] == 0 {
                        distinct += 1;
                    }
                    cnt[idx] += 1;
                }

                // 检查第一个窗口
                if distinct == d && cnt.iter().filter(|&&c| c > 0).all(|&c| c == target) {
                    ans = ans.max(len as i32);
                    // 如果已经找到当前长度的平衡子串，可以跳过检查该长度的其他窗口
                    // 但为了保持正确性，这里继续检查以找到更长的长度
                }

                // 滑动窗口
                for i in len..n {
                    // 移除左边界字符
                    let out_idx = (bytes[i - len] - b'a') as usize;
                    cnt[out_idx] -= 1;
                    if cnt[out_idx] == 0 {
                        distinct -= 1;
                    }

                    // 添加右边界字符
                    let in_idx = (bytes[i] - b'a') as usize;
                    if cnt[in_idx] == 0 {
                        distinct += 1;
                    }
                    cnt[in_idx] += 1;

                    if distinct == d && cnt.iter().filter(|&&c| c > 0).all(|&c| c == target) {
                        ans = ans.max(len as i32);
                    }
                }
            }
        }

        ans
    }
}
```
