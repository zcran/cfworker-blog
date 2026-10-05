---
title: "leetcode-位运算72"
date: 2026-09-24T10:33:38+08:00
tags: ["leetcode", "位运算"]
draft: false
---


## 使数组元素都变为零的最少操作次数

给你一个二维数组 queries，其中 queries[i] 形式为 [l, r]。每个 queries[i] 表示了一个元素范围从 l 到 r （包括 l 和 r ）的整数数组 nums 。

在一次操作中，你可以：

选择一个查询数组中的两个整数 a 和 b。
将它们替换为 floor(a / 4) 和 floor(b / 4)。

你的任务是确定对于每个查询，将数组中的所有元素都变为零的 最少 操作次数。返回所有查询结果的总和。


```
impl Solution {
    /// 计算区间 [0, num] 内所有整数变为 0 所需的总操作次数。
    ///
    /// 观察：对整数 x 反复执行 floor(x / 4)，直到变为 0。
    /// 定义 f(x) = 把 x 变为 0 所需的操作次数。
    /// 按 x 的二进制长度分组统计：长度为 i 位（即 x ∈ [2^(i-1), 2^i - 1]）的数，
    /// 每组内 f(x) 呈"阶梯"分布，可推导出组内总操作次数的公式。
    fn prefix_ops(num: i64) -> i64 {
        let mut total = 0i64;
        let mut bits = 1i64;      // 当前组的二进制位数
        let mut base = 1i64;      // 当前组起始值 2^(bits-1)

        while base <= num {
            // 当前组的右端点（不超过 num）
            let hi = (base * 2 - 1).min(num);
            // 组内 f(x) 的阶梯宽度为 2 的幂，累计方式为 ((bits + 1) / 2) 次
            // 这里用推导出的闭式：每层贡献 (bits+1)/2 次操作
            total += ((bits + 1) / 2) * (hi - base + 1);
            bits += 1;
            base *= 2;
        }
        total
    }

    pub fn min_operations(queries: Vec<Vec<i32>>) -> i64 {
        let mut res = 0i64;
        for q in &queries {
            let (l, r) = (q[0] as i64, q[1] as i64);
            // 区间 [l, r] 的总操作次数，再折半（每两次操作消一对，向上取整）
            let ops = Self::prefix_ops(r) - Self::prefix_ops(l - 1);
            res += (ops + 1) / 2;
        }
        res
    }
}
```
