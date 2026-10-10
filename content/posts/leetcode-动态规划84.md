---
title: "leetcode-动态规划84"
date: 2026-10-05T20:39:59+08:00
tags: ["leetcode", "动态规划"]
draft: false
---


## 压缩字符串 II

行程长度编码 是一种常用的字符串压缩方法，它将连续的相同字符（重复 2 次或更多次）替换为字符和表示字符计数的数字（行程长度）。例如，用此方法压缩字符串 "aabccc" ，将 "aa" 替换为 "a2" ，"ccc" 替换为` "c3" 。因此压缩后的字符串变为 "a2bc3" 。

注意，本问题中，压缩时没有在单个字符后附加计数 '1' 。

给你一个字符串 s 和一个整数 k 。你需要从字符串 s 中删除最多 k 个字符，以使 s 的行程长度编码长度最小。

请你返回删除最多 k 个字符后，s 行程长度编码的最小长度 。


```
impl Solution {
    pub fn get_length_of_optimal_compression(s: String, k: i32) -> i32 {
        let s = s.as_bytes();
        let n = s.len();
        let k = k as usize;
        const INF: i32 = i32::MAX / 2;

        // f[i][j]: 处理前 i 个字符，删除恰好 j 个字符时的最小行程编码长度
        let mut f = vec![vec![INF; k + 1]; n + 1];
        f[0][0] = 0;

        for i in 1..=n {
            for j in 0..=k.min(i) {
                // 删除当前字符 s[i-1]
                if j > 0 {
                    f[i][j] = f[i][j].min(f[i - 1][j - 1]);
                }

                // 保留当前字符，枚举最后一个街区起始位置 i0
                // 该街区为 s[i0-1..i]，全部为 s[i-1] 的字符
                let (mut same, mut diff) = (0, 0);
                for i0 in (1..=i).rev() {
                    if s[i0 - 1] == s[i - 1] {
                        same += 1;
                        let v = f[i0 - 1][j - diff] + Self::calc(same);
                        if v < f[i][j] {
                            f[i][j] = v;
                        }
                    } else {
                        diff += 1;
                        if diff > j {
                            break;
                        }
                    }
                }
            }
        }

        f[n][k]
    }

    // 计算长度为 len 的街区编码后的长度：单个字符为 1，否则为 1 + 位数
    #[inline]
    fn calc(len: usize) -> i32 {
        if len == 1 {
            1
        } else {
            1 + Self::digit_count(len)
        }
    }

    // 计算十进制位数
    #[inline]
    fn digit_count(mut x: usize) -> i32 {
        let mut cnt = 0;
        while x > 0 {
            cnt += 1;
            x /= 10;
        }
        cnt
    }
}
```
