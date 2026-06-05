---
title: "leetcode-组合数学48"
date: 2026-05-24T09:54:14+08:00
tags: ["leetcode", "组合数学"]
draft: false
---


## 恰好看到 K 个人的方向选择


给你三个整数 n、pos 和 k。

有 n 个人排成一排，下标从 0 到 n - 1。每个人 独立地 选择一个方向：

'L'：只对他们 右边 的人 可见
'R'：只对他们 左边 的人 可见

位于下标 pos 的人看其他人的方式如下：

一个 i < pos 的人可见当且仅当他们选择 'L'。
一个 i > pos 的人可见当且仅当他们选择 'R'。

返回可能的方向分配数量，使得位于下标 pos 的人 恰好 看到 k 个人。

由于答案可能很大，请将其对 10^9 + 7 取余 后返回。


```
// 模数，用于所有取模运算
const MOD: i64 = 1_000_000_007;
// 最大数据范围，根据题目设定（通常 n ≤ 100000）
const MX: usize = 100_001;

// 使用 lazy_static 确保全局静态数据只初始化一次，且线程安全
lazy_static::lazy_static! {
    // 阶乘数组：F[i] = i! % MOD
    static ref F: Vec<i64> = {
        let mut f = vec![1; MX];
        // 递推计算阶乘：F[i] = F[i-1] * i % MOD
        for i in 1..MX {
            f[i] = f[i-1] * i as i64 % MOD;
        }
        f
    };
    // 阶乘的逆元数组：INV_F[i] = (i!)^{-1} % MOD
    static ref INV_F: Vec<i64> = {
        let mut inv_f = vec![1; MX];
        // 先利用费马小定理计算最大阶乘的逆元：INV_F[MX-1] = ( (MX-1)! )^{MOD-2}
        inv_f[MX-1] = pow(F[MX-1], MOD-2);
        // 然后逆向递推：INV_F[i-1] = INV_F[i] * i % MOD
        // 因为 (i-1)! * i ≡ i! (mod MOD)，所以 (i-1)!^{-1} ≡ i!^{-1} * i
        for i in (1..MX).rev() {
            inv_f[i-1] = inv_f[i] * i as i64 % MOD;
        }
        inv_f
    };
}

/// 快速幂：计算 x^n (mod MOD)
/// 使用二进制指数分解，时间复杂度 O(log n)
fn pow(mut x: i64, mut n: i64) -> i64 {
    let mut res = 1;
    while n > 0 {
        if n % 2 == 1 {          // 当前二进制位为 1，乘入结果
            res = res * x % MOD;
        }
        x = x * x % MOD;         // x = x^2，准备下一位
        n /= 2;                  // 右移一位
    }
    res
}

/// 组合数 C(n, k) = n! / (k! * (n-k)!)
/// 使用预处理好的阶乘和逆元，O(1) 时间返回结果
fn comb(n: i32, k: i32) -> i64 {
    // 非法参数直接返回 0（组合数定义为 0）
    if k < 0 || k > n {
        return 0;
    }
    // 公式：F[n] * INV_F[k] % MOD * INV_F[n-k] % MOD
    F[n as usize] * INV_F[k as usize] % MOD * INV_F[(n - k) as usize] % MOD
}

impl Solution {
    /// 计算可见人数问题中的答案：C(n-1, k) * 2 % MOD
    /// 参数：
    /// - n: 总人数
    /// - _pos: 位置（未使用，可能为题目历史遗留）
    /// - k: 某些限制的个数
    /// 返回值：组合数乘以 2 后的模结果
    pub fn count_visible_people(n: i32, _pos: i32, k: i32) -> i32 {
        // 注意：n-1 可能小于 k，此时 comb 内部会返回 0
        (comb(n - 1, k) * 2 % MOD) as i32
    }
}
```
