---
title: "leetcode-枚举89"
date: 2026-07-09T10:05:03+08:00
tags: ["leetcode", "枚举"]
draft: false
---


## 大于目标字符串的最小字典序回文排列

给你两个长度均为 n 的字符串 s 和目标字符串 target，它们都由小写英文字母组成。

返回 字典序 最小的字符串 ，该字符串 既 是 s 的一个 回文 排列 ，又是字典序 严格 大于 target 的。如果不存在这样的排列，则返回一个空字符串。

如果字符串 a 和字符串 b 长度相同，在它们首次出现不同的位置上，字符串 a 处的字母在字母表中的顺序晚于字符串 b 处的对应字母，则字符串 a 在 字典序上严格大于 字符串 b。

排列 是指对字符串中所有字符的重新排列。

如果一个字符串从前向后读和从后向前读都一样，则该字符串是 回文 的。


```
impl Solution {
    pub fn lex_palindromic_permutation(s: String, target: String) -> String {
        let s_bytes = s.as_bytes();
        let target_bytes = target.as_bytes();
        let n = s_bytes.len();

        // 统计 s 中每个字母的出现次数
        let mut freq = [0; 26];
        for &ch in s_bytes {
            freq[(ch - b'a') as usize] += 1;
        }

        // 找出出现奇数次的字母（回文中心），最多只能有一个
        let mut mid_char = None;
        for i in 0..26 {
            if freq[i] % 2 == 1 {
                if mid_char.is_some() {
                    return String::new(); // 无法构成回文
                }
                mid_char = Some(i);
                freq[i] -= 1; // 预留一个给中间
            }
        }

        let half_len = n / 2;

        // 先尝试让左半部分与 target 的左半完全相同
        for i in 0..half_len {
            let idx = (target_bytes[i] - b'a') as usize;
            freq[idx] -= 2;
        }

        // 检查当前 freq 是否全非负
        let is_valid = |f: &[i32; 26]| f.iter().all(|&c| c >= 0);

        // 特殊情况：左半与 target 完全一致，检查右半是否能比 target 更大
        if is_valid(&freq) {
            let mut left = target_bytes[..half_len].to_vec();
            let mut right = left.clone();
            right.reverse();
            let mut candidate = left;
            if let Some(mid) = mid_char {
                candidate.push(b'a' + mid as u8);
            }
            candidate.extend(right);
            // 只需比较后半部分（中间字符及右半）
            if candidate[half_len..] > target_bytes[half_len..] {
                return String::from_utf8(candidate).unwrap();
            }
        }

        // 从后往前尝试增大 target 左半的某个位置
        for i in (0..half_len).rev() {
            let cur_idx = (target_bytes[i] - b'a') as usize;
            freq[cur_idx] += 2; // 撤销当前字符的消耗

            // 检查前缀 [0..i) 能否与 target 完全匹配
            if !is_valid(&freq) {
                continue;
            }

            // 尝试将当前位置换成更大的字母
            for j in (cur_idx + 1)..26 {
                if freq[j] == 0 {
                    continue;
                }

                // 使用更大的字母 j
                freq[j] -= 2;

                // 构造左半部分：前缀 + 当前位置 + 剩余字符（按字典序升序）
                let mut left_part = target_bytes[..i].to_vec();
                left_part.push(b'a' + j as u8);
                for k in 0..26 {
                    let count = freq[k] / 2; // 每个字符在左半出现的次数
                    for _ in 0..count {
                        left_part.push(b'a' + k as u8);
                    }
                }

                // 构建最终回文：左半 + 中间 + 右半（左半反转）
                let mut right_part = left_part.clone();
                right_part.reverse();
                let mut result = left_part;
                if let Some(mid) = mid_char {
                    result.push(b'a' + mid as u8);
                }
                result.extend(right_part);

                return String::from_utf8(result).unwrap();
            }
        }

        // 未找到更大的排列
        String::new()
    }
}
```
