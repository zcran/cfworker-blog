---
title: "leetcode-贪心171"
date: 2026-10-03T12:55:45+08:00
tags: ["leetcode", "贪心"]
draft: false
---


## 连接二进制片段得到的最大值

给你两个整数数组 nums1 和 nums0，每个数组的大小均为 n。

· nums1[i] 表示第 i 个片段中 '1' 的数量。
· nums0[i] 表示第 i 个片段中 '0' 的数量。

对于每个下标 i，构造一个由以下组成的二进制片段：

· nums1[i] 个 '1'，后跟
· nums0[i] 个 '0'。

你可以以任何方式 重新排列 这些 片段 的先后顺序。重新排列后，将所有片段 连接 起来形成一个单一的二进制字符串。

返回连接后的二进制字符串可能表示的 最大 整数值。

由于结果可能非常大，请返回对 10^9 + 7 取余 后的结果。


```
const MOD: i64 = 1_000_000_007;

impl Solution {
    pub fn max_value(nums1: Vec<i32>, nums0: Vec<i32>) -> i32 {
        // 贪心排序：纯 '1' 片段优先；其余 ones 降序、zeros 升序
        let mut frags: Vec<(i32, i32)> = nums1.into_iter().zip(nums0).collect();
        frags.sort_unstable_by_key(|&(ones, zeros)| (zeros != 0, -ones, zeros));

        // 拼接递推：ans = ((ans + 1) * 2^ones - 1) * 2^zeros
        // 每个片段只做一次快速幂，避免与总位数同阶的预处理开销
        let mut ans = 0i64;
        for (ones, zeros) in frags {
            let p1 = Self::pow2(ones as u64);
            ans = ((ans + 1) * p1 - 1) % MOD;
            ans = ans * Self::pow2(zeros as u64) % MOD;
        }

        ((ans % MOD + MOD) % MOD) as i32
    }

    #[inline]
    fn pow2(mut exp: u64) -> i64 {
        let mut base = 2i64;
        let mut res = 1i64;
        while exp > 0 {
            if exp & 1 == 1 {
                res = res * base % MOD;
            }
            base = base * base % MOD;
            exp >>= 1;
        }
        res
    }
}
```
