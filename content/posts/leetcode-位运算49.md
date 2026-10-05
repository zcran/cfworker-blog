---
title: "leetcode-位运算49"
date: 2026-09-24T10:33:37+08:00
tags: ["leetcode", "位运算"]
draft: false
---


## 在传球游戏中最大化函数值

给你一个长度为 n 下标从 0 开始的整数数组 receiver 和一个整数 k 。

总共有 n 名玩家，玩家 编号 互不相同，且为 [0, n - 1] 中的整数。这些玩家玩一个传球游戏，receiver[i] 表示编号为 i 的玩家会传球给编号为 receiver[i] 的玩家。玩家可以传球给自己，也就是说 receiver[i] 可能等于 i 。

你需要从 n 名玩家中选择一名玩家作为游戏开始时唯一手中有球的玩家，球会被传 恰好 k 次。

如果选择编号为 x 的玩家作为开始玩家，定义函数 f(x) 表示从编号为 x 的玩家开始，k 次传球内所有接触过球玩家的编号之 和 ，如果有玩家多次触球，则 累加多次 。换句话说， f(x) = x + receiver[x] + receiver[receiver[x]] + ... + receiver(k)[x] 。

你的任务时选择开始玩家 x ，目的是 最大化 f(x) 。

请你返回函数的 最大值 。

注意：receiver 可能含有重复元素。


```
impl Solution {
    pub fn get_max_function_value(receiver: Vec<i32>, k: i64) -> i64 {
        // 倍增（二进制提升）：
        // pa[t][x]  = 从 x 出发走 2^t 步后到达的玩家
        // sum[t][x] = 从 x 出发走 2^t 步过程中累加经过的玩家编号之和（不含起点）
        let n = receiver.len();
        let m = 64 - k.leading_zeros() as usize; // k 的二进制位数，也是需要的倍增层数

        let mut pa = vec![vec![0usize; n]; m];
        let mut sum = vec![vec![0i64; n]; m];

        for x in 0..n {
            pa[0][x] = receiver[x] as usize;
            sum[0][x] = receiver[x] as i64;
        }

        for t in 0..m - 1 {
            for x in 0..n {
                let mid = pa[t][x];
                pa[t + 1][x] = pa[t][mid];
                sum[t + 1][x] = sum[t][x] + sum[t][mid];
            }
        }

        // 对每个起点，用 k 的二进制分解跳跃累加，得到 f(x)，取最大值。
        let mut ans = 0;
        for start in 0..n {
            let mut total = start as i64;
            let mut x = start;
            let mut k = k;
            while k > 0 {
                let t = k.trailing_zeros() as usize; // 取 k 的最低位
                total += sum[t][x];
                x = pa[t][x];
                k &= k - 1; // 清除最低位
            }
            ans = ans.max(total);
        }
        ans
    }
}
```
