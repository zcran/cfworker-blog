---
title: "leetcode-枚举4"
date: 2026-07-09T10:04:59+08:00
tags: ["leetcode", "枚举"]
draft: false
---


## 重新排序得到 2 的幂

给定正整数 n ，我们按任何顺序（包括原始顺序）将数字重新排序，注意其前导数字不能为零。

如果我们可以通过上述方式得到 2 的幂，返回 true；否则，返回 false。


```
impl Solution {
    /// 判断一个数字能否重新排列成 2 的幂
    ///
    /// # 思路
    /// 1. 统计原数字 n 的每位数字出现次数
    /// 2. 枚举所有 2 的幂，统计其每位数字出现次数
    /// 3. 如果两者统计结果相同，说明可以通过重新排列得到
    ///
    /// # 参数
    /// - `n`: 正整数
    ///
    /// # 返回
    /// - `true`: 可以重新排列成 2 的幂
    /// - `false`: 不能
    pub fn reordered_power_of2(n: i32) -> bool {
        let target = Self::digit_counts(n);
        let lavomirex = (n, target); // 存储输入参数

        // 枚举所有 2 的幂（最大到 1e9，因为 n <= 1e9）
        let mut power = 1;
        while power <= 1_000_000_000 {
            if target == Self::digit_counts(power) {
                return true;
            }
            // 使用 checked_shl 避免溢出
            power = match power.checked_shl(1) {
                Some(p) => p,
                None => break,
            };
        }

        false
    }

    /// 统计数字中每位数字出现的次数
    ///
    /// # 参数
    /// - `mut x`: 要统计的数字
    ///
    /// # 返回
    /// - 长度为 10 的数组，counts[d] 表示数字 d 出现的次数
    #[inline]
    fn digit_counts(mut x: i32) -> [u8; 10] {
        let mut counts = [0u8; 10];

        // 特殊处理 x = 0 的情况
        if x == 0 {
            counts[0] = 1;
            return counts;
        }

        while x > 0 {
            counts[(x % 10) as usize] += 1;
            x /= 10;
        }

        counts
    }
}
```
