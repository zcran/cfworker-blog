---
title: "leetcode-前缀和118"
date: 2026-08-25T10:02:53+08:00
tags: ["leetcode", "前缀和"]
draft: false
---


## 插入一个字母的最大子序列数

给你一个由大写英文字母组成的字符串 s。

你可以在字符串的 任意 位置（包括字符串的开头或结尾）最多插入一个 大写英文字母。

返回在 最多插入一个字母 后，字符串中可以形成的 "LCT" 子序列的 最大 数量。

子序列 是从另一个字符串中删除某些字符（可以不删除）且不改变剩余字符顺序后得到的一个 非空 字符串。




```
impl Solution {
    pub fn num_of_subsequences(s: String) -> i64 {
        let s = s.as_bytes();
        let n = s.len();

        // 预处理右侧 T 数量和 CT 组合数
        let mut right_t = vec![0i64; n + 1];
        let mut right_ct = vec![0i64; n + 1];
        let mut cnt_t = 0;
        for i in (0..n).rev() {
            right_t[i] = right_t[i + 1];
            right_ct[i] = right_ct[i + 1];
            match s[i] {
                b'T' => {
                    cnt_t += 1;
                    right_t[i] += 1; // 当前 T 计入右侧
                }
                b'C' => {
                    right_ct[i] += cnt_t; // 当前 C 与右侧所有 T 形成 CT
                }
                _ => {}
            }
        }
        // 注意：right_t[i] 表示从 i 到末尾的 T 数量，right_ct[i] 表示从 i 到末尾的 CT 组合数

        // 从左向右扫描，维护左侧 L 和 LC 数量，同时计算原始 LCT 和三种插入增益
        let mut left_l = 0i64;
        let mut left_lc = 0i64;
        let mut original_lct = 0i64;
        let mut max_gain = 0i64;

        // 位置 p = 0..n，表示插入在 s[p] 之前（p=n 表示末尾）
        // 我们在遍历字符的过程中计算每个位置的增益
        for p in 0..=n {
            // 当前插入位置 p
            // 插入 'L' 的增益 = right_ct[p]
            let gain_l = right_ct[p];
            if gain_l > max_gain {
                max_gain = gain_l;
            }
            // 插入 'T' 的增益 = left_lc
            let gain_t = left_lc;
            if gain_t > max_gain {
                max_gain = gain_t;
            }
            // 插入 'C' 的增益 = left_l * right_t[p]
            let gain_c = left_l * right_t[p];
            if gain_c > max_gain {
                max_gain = gain_c;
            }

            // 如果 p == n，循环结束；否则处理字符 s[p] 更新左侧计数
            if p == n {
                break;
            }
            match s[p] {
                b'L' => {
                    // L 会增加左侧 L 数量，但不会产生 LC
                    left_l += 1;
                }
                b'C' => {
                    // C 会与左侧所有 L 形成 LC
                    left_lc += left_l;
                    // 注意：原始 LCT 不需要在这里加，遇到 T 时加
                }
                b'T' => {
                    // T 会与左侧所有 LC 形成 LCT
                    original_lct += left_lc;
                    // T 不会影响左侧 L/LC
                }
                _ => {}
            }
        }

        original_lct + max_gain
    }
}
```
