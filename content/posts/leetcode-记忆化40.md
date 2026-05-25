---
title: "leetcode-记忆化40"
date: 2026-05-06T20:22:35+08:00
tags: ["leetcode", "记忆化"]
draft: false
---


## 布尔运算

给定一个布尔表达式和一个期望的布尔结果 result，布尔表达式由 0 (false)、1 (true)、& (AND)、 | (OR) 和 ^ (XOR) 符号组成。实现一个函数，算出有几种可使该表达式得出 result 值的括号方法。

```
impl Solution {
    /// 计算布尔表达式 `s` 求值为 `result` (0 或 1) 的不同加括号方式的数量。
    ///
    /// 使用区间 DP，`dp[i][j][v]` 表示子表达式 `s[i..=j]` 求值为 `v` 的方案数。
    /// 表达式由数字（0/1）和操作符（&, |, ^）交替组成，因此 i, j 均为偶数下标。
    pub fn count_eval(s: String, result: i32) -> i32 {
        let bytes = s.as_bytes();
        let n = bytes.len();
        if n == 0 { return 0; }
        if n == 1 {
            return if (bytes[0] - b'0') as i32 == result { 1 } else { 0 };
        }

        // dp[left][right][value] 使用三维 Vec，初始全 0
        let mut dp = vec![vec![vec![0; 2]; n]; n];

        // 初始化长度为 1 的表达式（单个数字）
        for i in (0..n).step_by(2) {
            let val = (bytes[i] - b'0') as usize;
            dp[i][i][val] = 1;
        }

        // 枚举子表达式长度：从 3,5,7,... 到 n（表达式总长度为奇数）
        for len in (3..=n).step_by(2) {
            // 枚举子表达式起始位置（必须为偶数索引）
            for start in (0..n).step_by(2) {
                let end = start + len - 1;
                if end >= n { break; }

                // 枚举当前子表达式中的操作符位置（奇数索引）
                for op_idx in (start + 1..end).step_by(2) {
                    let left_end = op_idx - 1;
                    let right_start = op_idx + 1;
                    let (l0, l1) = (dp[start][left_end][0], dp[start][left_end][1]);
                    let (r0, r1) = (dp[right_start][end][0], dp[right_start][end][1]);

                    // 根据操作符更新 dp[start][end][0] 和 dp[start][end][1]
                    match bytes[op_idx] {
                        b'&' => {
                            // 结果为 0 的情况：00,01,10
                            dp[start][end][0] += l0 * r0 + l0 * r1 + l1 * r0;
                            // 结果为 1 的情况：11
                            dp[start][end][1] += l1 * r1;
                        }
                        b'|' => {
                            // 结果为 1 的情况：11,10,01
                            dp[start][end][1] += l1 * r1 + l1 * r0 + l0 * r1;
                            // 结果为 0 的情况：00
                            dp[start][end][0] += l0 * r0;
                        }
                        b'^' => {
                            // 结果为 1 的情况：01,10
                            dp[start][end][1] += l0 * r1 + l1 * r0;
                            // 结果为 0 的情况：00,11
                            dp[start][end][0] += l0 * r0 + l1 * r1;
                        }
                        _ => unreachable!(),
                    }
                }
            }
        }

        dp[0][n - 1][result as usize]
    }
}
```
