---
title: "leetcode-组合数学44"
date: 2026-05-24T09:54:14+08:00
tags: ["leetcode", "组合数学"]
draft: false
---


## 统计每一行选择互质整数的方案数


给你一个由正整数组成的 m x n 矩阵 mat。

返回一个整数，表示从 mat 的每一行中 恰好 选择一个整数，使得所有被选整数的 最大公约数 为 1 的选择方案数量。

由于答案可能非常大，请将其 模 10^9 + 7 后返回。


```
impl Solution {
    pub fn count_coprime(mat: Vec<Vec<i32>>) -> i32 {
        const MOD: i64 = 1_000_000_007;
        let m = mat.len();
        if m == 0 {
            return 0;
        }
        let mx = mat.iter()
            .flat_map(|row| row.iter().copied())
            .max()
            .unwrap_or(0) as usize;

        let mut memo = vec![vec![-1; mx + 1]; m];

        fn gcd(a: i32, b: i32) -> i32 {
            if b == 0 { a } else { gcd(b, a % b) }
        }

        fn dfs(
            mat: &[Vec<i32>],
            memo: &mut [Vec<i64>],
            i: i32,
            g: i32,
            mod_val: i64,
        ) -> i64 {
            if i < 0 {
                return if g == 1 { 1 } else { 0 };
            }
            let i = i as usize;
            let g = g as usize;
            if memo[i][g] != -1 {
                return memo[i][g];
            }
            let mut res = 0;
            for &x in &mat[i] {
                let new_g = if g == 0 { x } else { gcd(g as i32, x) };
                res = (res + dfs(mat, memo, i as i32 - 1, new_g, mod_val)) % mod_val;
            }
            memo[i][g] = res;
            res
        }

        dfs(&mat, &mut memo, (m - 1) as i32, 0, MOD) as i32
    }
}
```
