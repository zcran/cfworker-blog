---
title: "leetcode-组合数学43"
date: 2026-05-24T09:54:14+08:00
tags: ["leetcode", "组合数学"]
draft: false
---


## 位计数深度为 K 的整数数目 I


给你两个整数 n 和 k。

对于任意正整数 x，定义以下序列：

p0 = x
pi+1 = popcount(pi)，对于所有 i >= 0，其中 popcount(y) 是 y 的二进制表示中 1 的数量。

这个序列最终会达到值 1。

x 的 popcount-depth （位计数深度）定义为使得 pd = 1 的 最小 整数 d >= 0。

例如，如果 x = 7（二进制表示 "111"）。那么，序列是：7 → 3 → 2 → 1，所以 7 的 popcount-depth 是 3。

你的任务是确定范围 [1, n] 中 popcount-depth 恰好 等于 k 的整数数量。

返回这些整数的数量。


```
// 引入标准库中的 HashMap 和 Once（用于单次初始化）
use std::collections::HashMap;
use std::sync::Once;

// 最大比特位数：因为 10^15 约 2^50，所以 50 位足够
const MAX_BIT: usize = 50; // 10^15最多50个bit
// 最大支持的 popcount 深度？这里实际上是指需要查询的 k 的最大值，代码中用到 DEPTH_NUMS[0..=5]
const MAX_K: usize = 5;

// 用于单次初始化全局静态数据的同步原语
static INIT: Once = Once::new();

// 组合数表 COMB[n][m] = C(n, m)
static mut COMB: [[i64; MAX_BIT + 1]; MAX_BIT + 1] = [[0; MAX_BIT + 1]; MAX_BIT + 1];
// POPCOUNTS[i] 存储整数 i 的二进制表示中 1 的个数（popcount）
static mut POPCOUNTS: [i32; MAX_BIT + 1] = [0; MAX_BIT + 1];
// COUNTS[c] 存储 popcount 恰好等于 c 的数的个数（在 1..=MAX_BIT 范围内）
static mut COUNTS: [i32; MAX_BIT + 1] = [0; MAX_BIT + 1];
// DEPTHS 似乎与 popcount 的“深度”有关，但其定义并非标准，参见 calc_depth 中的赋值
static mut DEPTHS: [i32; MAX_BIT + 1] = [0; MAX_BIT + 1];
// DEPTH_NUMS[d] 存储所有深度（depth）为 d 的整数（不超过 MAX_BIT）
static mut DEPTH_NUMS: [Vec<i32>; MAX_K + 1] = [
    Vec::new(), Vec::new(), Vec::new(), Vec::new(), Vec::new(), Vec::new()
];

// 计算组合数表 COMB，使用递推公式 C(n, m) = C(n-1, m) + C(n-1, m-1)
fn calc_comb() {
    unsafe {
        COMB[0][0] = 1;
        for i in 1..=MAX_BIT {
            COMB[i][0] = 1;
            COMB[i][i] = 1;
            for j in 1..i {
                COMB[i][j] = COMB[i - 1][j] + COMB[i - 1][j - 1];
            }
        }
    }
}

// 预计算每个数（1..=MAX_BIT）的 popcount，并根据 popcount 构建“深度”层次
// 注意：这里 DEPTHS 的定义不是常规的，它递归地依赖于 popcount 的深度：DEPTHS[n] = DEPTHS[popcount(n)] + 1
// 这样最终 DEPTHS[n] 表示 n 经过反复计算 popcount 直到 1 所需的步数（即到 1 的距离）
// 例如：n=1 -> popcount=1 -> DEPTHS[1]=0；n=2 -> popcount=1 -> DEPTHS[2]=DEPTHS[1]+1=1
// 此函数同时填充 COUNTS（每个 popcount 值的数量）和 DEPTH_NUMS（每个深度对应的数字列表）
fn calc_depth() {
    unsafe {
        POPCOUNTS[1] = 1;
        COUNTS[1] += 1;
        DEPTHS[1] = 0;                       // 1 的深度为 0（因为 popcount(1)=1，但这里递归基是 1）
        DEPTH_NUMS[0].push(1);               // 深度 0 包含数字 1

        for n in 2..=MAX_BIT {
            // n & (n-1) 是去掉最低位的 1，所以 POPCOUNTS[(n & (n-1)) as usize] 加 1 得到 n 的 popcount
            let cnt = 1 + POPCOUNTS[(n & (n - 1)) as usize];
            POPCOUNTS[n] = cnt;
            COUNTS[cnt as usize] += 1;        // 统计具有该 popcount 的数有多少个
            // 当前数的深度 = 其 popcount 的深度 + 1
            let dep = DEPTHS[cnt as usize] + 1;
            DEPTHS[n] = dep;
            if dep <= MAX_K as i32 {
                DEPTH_NUMS[dep as usize].push(n as i32);
            }
        }
    }
}

// 初始化所有全局静态数据（仅执行一次）
fn init() {
    INIT.call_once(|| {
        calc_comb();
        calc_depth();
    });
}

impl Solution {
    /// 计算在 [1, n] 范围内，满足“深度（反复计算 popcount 直到 1 所需步数）”等于 k 的整数的个数。
    /// 注意：此处的“深度”即上述 calc_depth 中定义的 DEPTHS 值。
    /// 如果 k == 0，则只有数字 1 符合（深度为 0），返回 1。
    /// 否则，对于大于 MAX_BIT 的 n，利用组合数学计算。
    pub fn popcount_depth(n: i64, k: i32) -> i64 {
        init();  // 确保全局表已初始化
        let k = k as usize;

        if k == 0 {
            return 1; // 只有1的depth = 0
        }

        unsafe {
            // 如果 n 较小（<=50），可以直接从预计算的 DEPTH_NUMS 中统计
            if n <= MAX_BIT as i64 {
                return DEPTH_NUMS[k].iter()
                    .filter(|&&x| x <= n as i32)
                    .count() as i64;
            }

            // 对于较大的 n，先取所有深度为 k 的数（那些本身 <= MAX_BIT 的），它们的个数
            let mut nums = DEPTH_NUMS[k].len() as i64; // [1, 50]区间depth = k的数的个数

            // 对于每个深度为 k-1 的数 pcnt，需要统计 [1, n] 中 popcount 等于 pcnt 的数的个数，
            // 再减去已经包含在 [1, MAX_BIT] 中的部分（即 COUNTS[pcnt]）。
            for &pcnt in &DEPTH_NUMS[k - 1] {
                nums += Self::count_num(n, pcnt as i32) - COUNTS[pcnt as usize] as i64;
            }

            nums
        }
    }

    /// 辅助函数：计算在 [1, n] 范围内，二进制表示中 1 的个数恰好等于 pcnt 的整数的个数。
    /// 使用数位 DP（组合数学）方法，逐位统计。
    fn count_num(n: i64, pcnt: i32) -> i64 {
        let mut nums = 0;
        let mut bit = 1;
        let mut bits = 0;

        // 找到 n 的最高位（bit 是大于 n 的最小 2 的幂？实际上是找到最高位的 1 对应的权值）
        while bit <= n {
            bits += 1;
            bit <<= 1;
        }

        // 如果 n 的二进制位数小于所需的 1 的个数，不可能有符合条件的数
        if bits < pcnt {
            return 0;
        }

        bit >>= 1;          // bit 现在是 n 的最高位权值
        let mut pcnt = pcnt as usize;
        let mut bits = bits as usize;

        unsafe {
            while bit != 0 {
                if (n & bit) != 0 {
                    // 当前位为 1，我们可以选择这一位放 0，则剩余 bits-1 位中任选 pcnt 个位置放 1
                    nums += COMB[bits - 1][pcnt];
                    // 然后尝试这一位放 1，pcnt 减 1，继续处理低位
                    pcnt -= 1;
                    if pcnt == 0 {
                        // 如果已经凑够 1 的个数，则当前这种放法（高位与 n 完全一致）也满足，加 1
                        nums += 1;
                        break;
                    }
                }
                bit >>= 1;
                bits -= 1;
            }
        }

        nums
    }
}
```
