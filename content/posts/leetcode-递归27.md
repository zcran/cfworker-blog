---
title: "leetcode-递归27"
date: 2026-05-26T10:00:18+08:00
tags: ["leetcode", "递归"]
draft: false
---


## 数组元素的最小非零乘积


给你一个正整数 p 。你有一个下标从 1 开始的数组 nums ，这个数组包含范围 [1, 2p - 1] 内所有整数的二进制形式（两端都 包含）。你可以进行以下操作 任意 次：

从 nums 中选择两个元素 x 和 y  。
选择 x 中的一位与 y 对应位置的位交换。对应位置指的是两个整数 相同位置 的二进制位。

比方说，如果 x = 1101 且 y = 0011 ，交换右边数起第 2 位后，我们得到 x = 1111 和 y = 0001 。

请你算出进行以上操作 任意次 以后，nums 能得到的 最小非零 乘积。将乘积对 10^9 + 7 取余 后返回。

注意：答案应为取余 之前 的最小值。

```
const MOD: i64 = 1_000_000_007;

impl Solution {
    /// 计算最小非零乘积
    ///
    /// # 问题说明
    /// 给定一个整数 p，考虑范围 [1, 2^p - 1] 内的所有整数，
    /// 可以通过交换任意两个数的二进制位来重新排列这些数，
    /// 目标是使所有数的乘积最小且非零。
    ///
    /// # 数学原理
    /// 经过数学推导，最小非零乘积为：
    /// (2^p - 2)^(2^(p-1) - 1) × (2^p - 1) mod MOD
    ///
    /// 其中：
    /// - x = 2^p - 1 是最大值
    /// - x - 1 = 2^p - 2 是其他数的目标值
    /// - 指数 y = 2^(p-1) - 1 是 (x-1) 出现的次数
    ///
    /// # 复杂度
    /// - 时间复杂度：O(p) - 快速幂对数级别
    /// - 空间复杂度：O(1)
    ///
    /// # 示例
    /// ```
    /// assert_eq!(Solution::min_non_zero_product(1), 1);
    /// // [1] → 乘积 = 1
    ///
    /// assert_eq!(Solution::min_non_zero_product(2), 6);
    /// // [1,2,3] 重排后 [1,1,6] 不对，实际应得到 6
    ///
    /// assert_eq!(Solution::min_non_zero_product(3), 1512);
    /// ```
    pub fn min_non_zero_product(p: i32) -> i32 {
        // p = 1 时，只有一个数 [1]，乘积为 1
        if p == 1 {
            return 1;
        }

        // 计算 x = 2^p - 1（最大值）
        let max_val = (Self::pow_mod(2, p as i64) - 1 + MOD) % MOD;

        // 计算指数 y = 2^(p-1) - 1
        let exponent = (1i64 << (p - 1)) - 1;

        // 结果 = (x-1)^(y) × x mod MOD
        let result = (Self::pow_mod(max_val - 1, exponent) * max_val) % MOD;

        result as i32
    }

    /// 快速幂取模
    ///
    /// 使用二进制指数分解法，计算 base^exp mod MOD
    ///
    /// # 参数
    /// - `base`: 底数（已经取模）
    /// - `mut exp`: 指数（非负）
    ///
    /// # 返回
    /// - base^exp mod MOD
    ///
    /// # 算法说明
    /// 将指数表示为二进制，例如 13 = 1101₂ = 8+4+1
    /// base^13 = base^8 × base^4 × base^1
    /// 通过反复平方可以高效计算
    #[inline]
    fn pow_mod(mut base: i64, mut exp: i64) -> i64 {
        let mut result = 1i64;

        while exp > 0 {
            // 如果当前二进制位为 1，乘入结果
            if exp & 1 == 1 {
                result = (result * base) % MOD;
            }
            // 底数平方，准备下一位
            base = (base * base) % MOD;
            // 右移一位，处理下一个二进制位
            exp >>= 1;
        }

        result
    }
}
```
