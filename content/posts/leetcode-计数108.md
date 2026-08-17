---
title: "leetcode-计数108"
date: 2026-08-01T10:40:56+08:00
tags: ["leetcode", "计数"]
draft: false
---


## 字符串转换后的长度 II

给你一个由小写英文字母组成的字符串 s，一个整数 t 表示要执行的 转换 次数，以及一个长度为 26 的数组 nums。每次 转换 需要根据以下规则替换字符串 s 中的每个字符：

将 s[i] 替换为字母表中后续的 nums[s[i] - 'a'] 个连续字符。例如，如果 s[i] = 'a' 且 nums[0] = 3，则字符 'a' 转换为它后面的 3 个连续字符，结果为 "bcd"。

如果转换超过了 'z'，则 回绕 到字母表的开头。例如，如果 s[i] = 'y' 且 nums[24] = 3，则字符 'y' 转换为它后面的 3 个连续字符，结果为 "zab"。

返回 恰好 执行 t 次转换后得到的字符串的 长度。

由于答案可能非常大，返回其对 10^9 + 7 取余的结果。


```
const MOD: i64 = 1_000_000_007;

impl Solution {
    pub fn length_after_transformations(s: String, t: i32, nums: Vec<i32>) -> i32 {
        // 构造转移矩阵：trans[i][j] = 1 表示字符 j 经过一次转换会贡献到字符 i
        let mut trans = [[0i64; 26]; 26];
        for j in 0..26 {
            for k in 1..=nums[j] as usize {
                trans[(j + k) % 26][j] = 1;
            }
        }

        // 矩阵快速幂
        let mat_pow = Self::mat_pow(trans, t);

        // 初始频率向量
        let mut freq = [0i64; 26];
        for b in s.bytes() {
            freq[(b - b'a') as usize] += 1;
        }

        // 计算最终总长度：sum(mat_pow[i][j] * freq[j])
        let mut ans = 0i64;
        for i in 0..26 {
            for j in 0..26 {
                ans = (ans + mat_pow[i][j] * freq[j]) % MOD;
            }
        }
        ans as i32
    }

    // 26x26 矩阵乘法（i-k-j 顺序更缓存友好，跳过零元素）
    fn mat_mul(a: [[i64; 26]; 26], b: [[i64; 26]; 26]) -> [[i64; 26]; 26] {
        let mut c = [[0i64; 26]; 26];
        for i in 0..26 {
            for k in 0..26 {
                if a[i][k] == 0 { continue; }
                for j in 0..26 {
                    c[i][j] = (c[i][j] + a[i][k] * b[k][j]) % MOD;
                }
            }
        }
        c
    }

    // 矩阵快速幂
    fn mat_pow(mut base: [[i64; 26]; 26], mut exp: i32) -> [[i64; 26]; 26] {
        let mut res = [[0i64; 26]; 26];
        for i in 0..26 {
            res[i][i] = 1;  // 单位矩阵
        }
        while exp > 0 {
            if exp & 1 == 1 {
                res = Self::mat_mul(res, base);
            }
            base = Self::mat_mul(base, base);
            exp >>= 1;
        }
        res
    }
}
```
