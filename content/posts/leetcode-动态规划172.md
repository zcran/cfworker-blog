---
title: "leetcode-动态规划172"
date: 2026-10-05T20:40:06+08:00
tags: ["leetcode", "动态规划"]
draft: false
---


## 统计网格路径中好整数的数目

给你两个整数 l 和 r，以及一个由 恰好 三个 'D' 字符和三个 'R' 字符组成的字符串 directions。

对于范围 [l, r]（包含边界）内的每个整数 x，执行以下步骤：

1. 如果 x 的位数少于 16 位，请在其左侧填充 前导零 ，使其成为 16 位的字符串。
2. 将这 16 个数字以 行优先 的顺序放入一个 4 × 4 的网格中（前 4 个数字从左到右构成第一行，接下来的 4 个数字构成第二行，依此类推）。
3. 从左上角单元格（row = 0，column = 0）开始，按顺序应用 directions 中的 6 个字符：
    · 'D' 使行数加 1。
    · 'R' 使列数加 1。
4. 记录沿路径访问的数字序列（包括起始单元格），生成一个长度为 7 的序列。

如果记录的序列是 非递减 的，则认为整数 x 是一个 好 整数。

返回一个整数，表示在范围 [l, r] 内好整数的数量。


```
use std::sync::LazyLock;

const MAX_M: usize = 7;

/// 预处理组合数 comb[i][j] = C(i, j)
static COMB: LazyLock<[[i64; MAX_M + 1]; MAX_M + 10]> = LazyLock::new(|| {
    let mut comb = [[0i64; MAX_M + 1]; MAX_M + 10];
    for i in 0..MAX_M + 10 {
        comb[i][0] = 1;
        for j in 1..=i.min(MAX_M) {
            comb[i][j] = comb[i - 1][j - 1] + comb[i - 1][j];
        }
    }
    comb
});

impl Solution {
    pub fn count_good_integers_on_path(l: i64, r: i64, directions: String) -> i64 {
        let comb = &*COMB;

        // 上界取 r+1，用 [0, r+1) 减去 [0, l)，即 [l, r]
        let high_s = (r + 1).to_string();
        let n = high_s.len();
        let low_s = {
            let s = l.to_string();
            format!("{}{}", "0".repeat(n - s.len()), s)
        };
        let high_digits: Vec<i32> = high_s.bytes().map(|b| (b - b'0') as i32).collect();
        let low_digits: Vec<i32> = low_s.bytes().map(|b| (b - b'0') as i32).collect();

        // 标记路径上经过的格子（只关心最后 n 个格子）
        let mut in_path = vec![false; n];
        let mut pos: i64 = n as i64 - 16; // 左下角对应下标 n-16
        for d in directions.bytes() {
            if pos >= 0 {
                in_path[pos as usize] = true;
            }
            pos += if d == b'R' { 1 } else { 4 }; // 'D' 等价于右移 4 个位置
        }
        in_path[n - 1] = true; // 终点一定在路径中

        // suf[i] = 后缀 [i, n-1] 中在路径上的格子数
        let mut suf = vec![0i32; n + 1];
        for i in (0..n).rev() {
            suf[i] = suf[i + 1] + in_path[i] as i32;
        }

        // 预处理 10 的幂
        let mut pow10 = vec![1i64; n + 1];
        for i in 1..=n {
            pow10[i] = pow10[i - 1] * 10;
        }

        // 计算 [0, s] 中好整数的个数（s 为与 high_s 等长的数字序列）
        let solve = |s: &[i32]| -> i64 {
            let mut res: i64 = 0;
            let mut pre: i32 = 0;
            for i in 0..n {
                let hi = s[i];
                let m = suf[i + 1] as usize;

                if !in_path[i] {
                    // 当前位不在路径上，可任填 0..=hi
                    res += hi as i64 * comb[m + 9 - pre as usize][m] * pow10[n - 1 - i - m];
                    continue;
                }

                // 当前位在路径上，需要满足非递减约束
                if hi < pre {
                    break;
                }
                // 当前位填 0..=hi-1 的方案数：C(m+10-pre, m+1) - C(m+10-hi, m+1)
                res += (comb[m + 10 - pre as usize][m + 1] - comb[m + 10 - hi as usize][m + 1])
                    * pow10[n - 1 - i - m];
                pre = hi; // 这一位填 hi，继续
            }
            res
        };

        solve(&high_digits) - solve(&low_digits)
    }
}
```
