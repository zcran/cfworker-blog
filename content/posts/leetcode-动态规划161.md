---
title: "leetcode-动态规划161"
date: 2026-10-05T20:40:05+08:00
tags: ["leetcode", "动态规划"]
draft: false
---


## 统计和为 N 的无零数对

一个 无零 整数是一个十进制表示中 不包含数字 0 的 正 整数。

给定一个整数 n，计算满足以下条件的数对 (a, b) 的数量：

a 和 b 都是 无零 整数。
a + b = n

返回一个整数，表示此类数对的数量。


```
impl Solution {
    pub fn count_no_zero_pairs(n: i64) -> i64 {
        let s: Vec<u8> = n.to_string().bytes().map(|b| b - b'0').collect();
        let m = s.len();

        // 两个 1~9 的整数和为 target 的方案数
        // 枚举 a 从 1 到 9，b = target - a 必须也在 1..=9
        let two_sum_ways = |target: i32| -> i64 {
            if target < 2 || target > 18 {
                0
            } else {
                // target-1 与 19-target 取较小值即为方案数
                (target - 1).min(19 - target) as i64
            }
        };

        // memo[i][borrowed][is_num]，-1 表示未计算
        // borrowed：低位是否向当前位借了 1
        // is_num：之前填的数位中，两个数是否都已避开前导零（即都已开始）
        let mut memo = vec![[[-1i64; 2]; 2]; m];

        fn dfs(
            i: isize,
            borrowed: bool,
            is_num: bool,
            s: &[u8],
            m: usize,
            memo: &mut Vec<[[i64; 2]; 2]>,
            two_sum_ways: &dyn Fn(i32) -> i64,
        ) -> i64 {
            if i < 0 {
                // 借位必须已还清
                return if borrowed { 0 } else { 1 };
            }

            let bi = borrowed as usize;
            let ni = is_num as usize;
            if memo[i as usize][bi][ni] != -1 {
                return memo[i as usize][bi][ni];
            }

            let d = s[i as usize] as i32 - borrowed as i32;
            let mut res: i64 = 0;

            // 情况一：两个数位都不为 0（两个数都已开始填）
            if is_num {
                // 不向高位借 1：要求 d >= 2 且 d <= 18
                res += dfs(i - 1, false, true, s, m, memo, two_sum_ways) * two_sum_ways(d);
                // 向高位借 1：相当于和为 d + 10
                res += dfs(i - 1, true, true, s, m, memo, two_sum_ways) * two_sum_ways(d + 10);
            }

            // 情况二：其中一个数位填前导零（不能是最低位，否则另一个数会以 0 结尾）
            if (i as usize) < m - 1 {
                if d != 0 {
                    // d 为负时必须向高位借 1
                    // 若 is_num 为真，两个数交换对称，方案数乘以 2
                    let mult = if is_num { 2 } else { 1 };
                    res += dfs(i - 1, d < 0, false, s, m, memo, two_sum_ways) * mult;
                } else if i == 0 {
                    // 两个数位都填 0，只有在最低位（i == 0）时才有解
                    res += 1;
                }
            }

            memo[i as usize][bi][ni] = res;
            res
        }

        dfs(
            m as isize - 1,
            false,
            true,
            &s,
            m,
            &mut memo,
            &two_sum_ways,
        )
    }
}
```
