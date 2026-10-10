---
title: "leetcode-动态规划154"
date: 2026-10-05T20:40:04+08:00
tags: ["leetcode", "动态规划"]
draft: false
---


## 移除相邻字符后字典序最小的字符串

给你一个由小写英文字母组成的字符串 s。

你可以进行以下操作任意次（包括零次）：

移除字符串中 任意 一对 相邻 字符，这两个字符在字母表中是 连续 的，无论顺序如何（例如，'a' 和 'b'，或者 'b' 和 'a'）。
将剩余字符左移以填补空隙。

返回经过最优操作后可以获得的 字典序最小 的字符串。

当且仅当在第一个不同的位置上，字符串 a 的字母在字母表中出现的位置早于字符串 b 的字母，则认为字符串 a 的 字典序小于 字符串 b，。
如果 min(a.length, b.length) 个字符都相同，则较短的字符串字典序更小。

注意：字母表被视为循环的，因此 'a' 和 'z' 也视为连续。


```
impl Solution {
    pub fn lexicographically_smallest_string(s: String) -> String {
        let s = s.as_bytes();
        let n = s.len();

        // 判断两个字符在循环字母表中是否相邻（距离为 1 或 25）
        let is_consecutive = |x: u8, y: u8| -> bool {
            let d = (x as i32 - y as i32).unsigned_abs();
            d == 1 || d == 25
        };

        // can_be_empty[i][j] 表示子串 s[i..=j] 能否完全消除为空串
        // 用 u8 代替 bool，提升缓存友好性
        let mut can_be_empty = vec![vec![0u8; n]; n];

        // 从右往左、按长度递增计算
        for i in (0..n.saturating_sub(1)).rev() {
            // 长度为奇数的区间不可能被完全消除（每次消除 2 个字符）
            // j 从 i+1 开始，步长为 2，保证区间长度为偶数
            for j in (i + 1..n).step_by(2) {
                // 性质 2：s[i] 与 s[j] 相邻，且中间部分可完全消除
                let inner_empty = if j == i + 1 {
                    true
                } else {
                    can_be_empty[i + 1][j - 1] != 0
                };
                if is_consecutive(s[i], s[j]) && inner_empty {
                    can_be_empty[i][j] = 1;
                    continue;
                }

                // 性质 3：区间可拆分为两个可完全消除的子区间
                // k 从 i+1 开始，步长为 2，保证 [i..=k] 与 [k+1..=j] 长度均为偶数
                let mut k = i + 1;
                while k + 1 < j {
                    if can_be_empty[i][k] != 0 && can_be_empty[k + 1][j] != 0 {
                        can_be_empty[i][j] = 1;
                        break;
                    }
                    k += 2;
                }
            }
        }

        // f[i] 表示后缀 s[i..] 经过最优操作后能得到的最小字典序字符串
        // 用 Vec<u8> 存字符串，最后统一转 String，避免频繁的 String 拼接
        let mut f: Vec<Vec<u8>> = vec![Vec::new(); n + 1];

        for i in (0..n).rev() {
            // 选项 1：保留 s[i]
            let mut best = Vec::with_capacity(1 + f[i + 1].len());
            best.push(s[i]);
            best.extend_from_slice(&f[i + 1]);

            // 选项 2：消除 s[i..=j]（j 与 i 同奇偶以保证长度为偶数）
            let mut j = i + 1;
            while j < n {
                if can_be_empty[i][j] != 0 {
                    // 消除后直接使用 f[j+1]
                    if f[j + 1] < best {
                        best = f[j + 1].clone();
                    }
                }
                j += 2;
            }

            f[i] = best;
        }

        // 转换为 String（字节均为 ASCII 小写字母，安全）
        String::from_utf8(f[0].clone()).unwrap()
    }
}
```
