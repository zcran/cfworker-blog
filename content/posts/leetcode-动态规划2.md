---
title: "leetcode-动态规划2"
date: 2026-10-05T20:39:53+08:00
tags: ["leetcode", "动态规划"]
draft: false
---


## 扰乱字符串

使用下面描述的算法可以扰乱字符串 s 得到字符串 t ：
1. 如果字符串的长度为 1 ，算法停止
2. 如果字符串的长度 > 1 ，执行下述步骤：
    · 在一个随机下标处将字符串分割成两个非空的子字符串。即，如果已知字符串 s ，则可以将其分成两个子字符串 x 和 y ，且满足 s = x + y 。
    · 随机 决定是要「交换两个子字符串」还是要「保持这两个子字符串的顺序不变」。即，在执行这一步骤之后，s 可能是 s = x + y 或者 s = y + x 。
    · 在 x 和 y 这两个子字符串上继续从步骤 1 开始递归执行此算法。

给你两个 长度相等 的字符串 s1 和 s2，判断 s2 是否是 s1 的扰乱字符串。如果是，返回 true ；否则，返回 false 。


```
use std::collections::HashMap;

impl Solution {
    pub fn is_scramble(s1: String, s2: String) -> bool {
        let s1 = s1.as_bytes();
        let s2 = s2.as_bytes();
        let n = s1.len();

        if s1.len() != s2.len() {
            return false;
        }

        // 记忆化：区间对 (i1, i2, len) -> 是否为扰乱字符串
        // 用 (i1, i2, len) 替代四个下标，减少键的维度和冗余
        let mut memo: HashMap<(usize, usize, usize), bool> = HashMap::new();

        fn dfs(
            s1: &[u8],
            i1: usize,
            s2: &[u8],
            i2: usize,
            len: usize,
            memo: &mut HashMap<(usize, usize, usize), bool>,
        ) -> bool {
            if let Some(&v) = memo.get(&(i1, i2, len)) {
                return v;
            }

            // 子串完全相同，无需进一步划分
            if s1[i1..i1 + len] == s2[i2..i2 + len] {
                memo.insert((i1, i2, len), true);
                return true;
            }

            // 字符频次不同，一定不是扰乱字符串
            let mut cnt = [0i32; 26];
            for k in 0..len {
                cnt[(s1[i1 + k] - b'a') as usize] += 1;
                cnt[(s2[i2 + k] - b'a') as usize] -= 1;
            }
            if cnt.iter().any(|&c| c != 0) {
                memo.insert((i1, i2, len), false);
                return false;
            }

            // 枚举分割点，尝试两种拼接方式：不交换 / 交换
            for k in 1..len {
                // 不交换：s1[..k] 对应 s2[..k]，s1[k..] 对应 s2[k..]
                if dfs(s1, i1, s2, i2, k, memo)
                    && dfs(s1, i1 + k, s2, i2 + k, len - k, memo)
                {
                    memo.insert((i1, i2, len), true);
                    return true;
                }
                // 交换：s1[..k] 对应 s2[len-k..]，s1[k..] 对应 s2[..len-k]
                if dfs(s1, i1, s2, i2 + len - k, k, memo)
                    && dfs(s1, i1 + k, s2, i2, len - k, memo)
                {
                    memo.insert((i1, i2, len), true);
                    return true;
                }
            }

            memo.insert((i1, i2, len), false);
            false
        }

        dfs(s1, 0, s2, 0, n, &mut memo)
    }
}
```
