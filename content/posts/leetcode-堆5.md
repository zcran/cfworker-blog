---
title: "leetcode-堆5"
date: 2026-08-21T09:54:10+08:00
tags: ["leetcode", "堆"]
draft: false
---


## 强密码检验器

满足以下条件的密码被认为是强密码：

· 由至少 6 个，至多 20 个字符组成。
· 包含至少 一个小写 字母，至少 一个大写 字母，和至少 一个数字 。
· 不包含连续三个重复字符 (比如 "Baaabb0" 是弱密码, 但是 "Baaba0" 是强密码)。

给你一个字符串 password ，返回 将 password 修改到满足强密码条件需要的最少修改步数。如果 password 已经是强密码，则返回 0 。

在一步修改操作中，你可以：

· 插入一个字符到 password ，
· 从 password 中删除一个字符，或
· 用另一个字符来替换 password 中的某个字符。


```
impl Solution {
    /// 计算将密码修改为强密码所需的最少操作步数
    ///
    /// 强密码条件：
    /// 1. 长度 6~20
    /// 2. 包含至少一个小写、一个大写、一个数字
    /// 3. 不含连续三个重复字符
    ///
    /// 操作：插入、删除、替换各计 1 步
    pub fn strong_password_checker(password: String) -> i32 {
        let n = password.len();
        let s = password.as_bytes();

        // ---- 统计缺失的字符类型 ----
        let mut miss_types = 3;
        for &c in s {
            if c.is_ascii_lowercase() { miss_types -= 1; break; }
        }
        for &c in s {
            if c.is_ascii_uppercase() { miss_types -= 1; break; }
        }
        for &c in s {
            if c.is_ascii_digit() { miss_types -= 1; break; }
        }

        // ---- 统计连续重复序列 ----
        let mut replace = 0; // 打破连续重复所需的替换次数
        let mut one_seq = 0; // 长度 % 3 == 0 的序列数（1删可替代1替换）
        let mut two_seq = 0; // 长度 % 3 == 1 的序列数（2删可替代1替换）

        let mut i = 0;
        while i < n {
            let mut j = i + 1;
            while j < n && s[j] == s[i] {
                j += 1;
            }
            let len = j - i;
            if len >= 3 {
                replace += len / 3;
                match len % 3 {
                    0 => one_seq += 1,
                    1 => two_seq += 1,
                    _ => {}
                }
            }
            i = j;
        }

        // ---- 情况 1: 长度不足 ----
        if n < 6 {
            // 插入既可补长度也可补类型，取较大值
            return miss_types.max(6 - n as i32);
        }

        // ---- 情况 2: 长度合法 ----
        if n <= 20 {
            // 替换操作可同时修复连续重复和缺失类型
            return miss_types.max(replace as i32);
        }

        // ---- 情况 3: 长度超过 20，必须用删除 ----
        let delete = (n - 20) as i32;

        // 优先在 len%3==0 的序列上删除（效率最高：1删 = 1替换）
        replace -= one_seq.min(delete as usize);

        // 其次在 len%3==1 的序列上删除（2删 = 1替换）
        let rem = delete as usize - one_seq.min(delete as usize);
        replace -= two_seq.min(rem / 2);

        // 最后在剩余序列上删除（3删 = 1替换）
        let rem = rem.saturating_sub(two_seq * 2);
        replace -= rem / 3;

        delete + miss_types.max(replace as i32)
    }
}
```
