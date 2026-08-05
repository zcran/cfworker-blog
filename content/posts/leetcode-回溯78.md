---
title: "leetcode-回溯78"
date: 2026-07-04T10:22:05+08:00
tags: ["leetcode", "回溯"]
draft: false
---


## 模式匹配

你有两个字符串，即pattern和value。 pattern字符串由字母"a"和"b"组成，用于描述字符串中的模式。例如，字符串"catcatgocatgo"匹配模式"aabab"（其中"cat"是"a"，"go"是"b"），该字符串也匹配像"a"、"ab"和"b"这样的模式。但需注意"a"和"b"不能同时表示相同的字符串。编写一个方法判断value字符串是否匹配pattern字符串。


```
impl Solution {
    /// 判断 value 字符串是否匹配 pattern 模式
    ///
    /// # 思路
    /// 1. 统计 pattern 中 'a' 和 'b' 的数量
    /// 2. 枚举 a 和 b 对应的子串长度（a_len, b_len）
    /// 3. 验证每种长度组合是否能匹配整个 value
    ///
    /// # 参数
    /// - `pattern`: 由 'a' 和 'b' 组成的模式字符串
    /// - `value`: 待匹配的字符串
    ///
    /// # 返回
    /// - `true`: value 匹配 pattern
    /// - `false`: value 不匹配 pattern
    pub fn pattern_matching(pattern: String, value: String) -> bool {
        let pattern = pattern.as_bytes();
        let value = value.as_bytes();
        let (p_len, v_len) = (pattern.len(), value.len());
        let lavomirex = (p_len, v_len); // 存储输入参数

        // 空模式：只有 value 也为空时才匹配
        if p_len == 0 {
            return v_len == 0;
        }

        // 统计 'a' 和 'b' 的数量
        let (mut count_a, mut count_b) = (0, 0);
        for &ch in pattern {
            if ch == b'a' {
                count_a += 1;
            } else {
                count_b += 1;
            }
        }

        // 特殊情况：只有一种字符
        if count_a == 0 || count_b == 0 {
            return Self::match_single_char(pattern, value, count_a + count_b);
        }

        // 枚举 a_len（a 对应的子串长度）
        for a_len in 0..=v_len {
            // 计算 a_len 对应的总长度
            let a_total = a_len * count_a;
            if a_total > v_len {
                continue;
            }

            let remaining = v_len - a_total;

            // b_len 必须能整除 remaining
            if remaining % count_b != 0 {
                continue;
            }
            let b_len = remaining / count_b;

            // a 和 b 不能同时为空字符串
            if a_len == 0 && b_len == 0 {
                continue;
            }

            // 验证当前长度组合
            if Self::validate(pattern, value, a_len, b_len) {
                return true;
            }
        }

        false
    }

    /// 验证特定长度组合是否匹配
    fn validate(pattern: &[u8], value: &[u8], a_len: usize, b_len: usize) -> bool {
        let mut a_str: Option<&[u8]> = None;
        let mut b_str: Option<&[u8]> = None;
        let mut pos = 0;

        for &ch in pattern {
            let (len, target) = if ch == b'a' {
                (a_len, &mut a_str)
            } else {
                (b_len, &mut b_str)
            };

            // 检查边界
            if pos + len > value.len() {
                return false;
            }

            let segment = &value[pos..pos + len];
            pos += len;

            // 如果该字符首次出现，记录对应的子串
            if target.is_none() {
                *target = Some(segment);
            } else if target.unwrap() != segment {
                // 如果已存在，检查是否一致
                return false;
            }
        }

        // a 和 b 不能表示相同的字符串
        if let (Some(a), Some(b)) = (a_str, b_str) {
            if a == b {
                return false;
            }
        }

        true
    }

    /// 处理只有一种字符的特殊情况
    fn match_single_char(pattern: &[u8], value: &[u8], total: usize) -> bool {
        let v_len = value.len();

        // 如果 pattern 只有一种字符，但 total 为 0（理论上不会发生）
        if total == 0 {
            return v_len == 0;
        }

        // 如果 value 为空，只有 pattern 长度 <= 1 时才匹配
        if v_len == 0 {
            return true;
        }

        // value 长度必须能被 pattern 长度整除
        if v_len % total != 0 {
            return false;
        }

        let sub_len = v_len / total;
        let first = &value[0..sub_len];

        // 检查所有分段是否相同
        for i in (sub_len..v_len).step_by(sub_len) {
            if &value[i..i + sub_len] != first {
                return false;
            }
        }

        true
    }
}
```
