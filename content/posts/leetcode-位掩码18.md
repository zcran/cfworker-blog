---
title: "leetcode-位掩码18"
date: 2026-05-18T10:27:59+08:00
tags: ["leetcode", "位掩码"]
draft: false
---


## N 次操作后的最大分数和

给你 nums ，它是一个大小为 2 * n 的正整数数组。你必须对这个数组执行 n 次操作。

在第 i 次操作时（操作编号从 1 开始），你需要：

· 选择两个元素 x 和 y 。
· 获得分数 i * gcd(x, y) 。
· 将 x 和 y 从 nums 中删除。

请你返回 n 次操作后你能获得的分数和最大为多少。

函数 gcd(x, y) 是 x 和 y 的最大公约数。

```
impl Solution {
    pub fn max_score(nums: Vec<i32>) -> i32 {
        let n = nums.len();
        let full = 1 << n;
        let mut dp = vec![0; full];

        // 预处理所有数对的 gcd
        let mut gcd_table = vec![vec![0; n]; n];
        for i in 0..n {
            for j in i + 1..n {
                let g = Self::gcd(nums[i], nums[j]);
                gcd_table[i][j] = g;
                gcd_table[j][i] = g;
            }
        }

        // 遍历所有掩码，只关心已选偶数个元素的状态
        for mask in 0..full {
            let cnt = mask.count_ones() as usize;
            if cnt % 2 == 1 {
                continue;
            }
            let step = (cnt / 2) as i32 + 1;
            // 枚举所有未配对的元素对 (i, j)
            for i in 0..n {
                if (mask >> i) & 1 == 1 {
                    continue;
                }
                for j in i + 1..n {
                    if (mask >> j) & 1 == 1 {
                        continue;
                    }
                    let new_mask = mask | (1 << i) | (1 << j);
                    let score = dp[mask] + step * gcd_table[i][j];
                    dp[new_mask] = dp[new_mask].max(score);
                }
            }
        }

        dp[full - 1]
    }

    fn gcd(a: i32, b: i32) -> i32 {
        if b == 0 { a } else { Self::gcd(b, a % b) }
    }
}
```
