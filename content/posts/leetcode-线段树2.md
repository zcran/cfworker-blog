---
title: "leetcode-线段树2"
date: 2026-06-19T09:37:24+08:00
tags: ["leetcode", "线段树"]
draft: false
---


## 奇妙序列

请你实现三个 API append，addAll 和 multAll 来实现奇妙序列。

请实现 Fancy 类 ：

· Fancy() 初始化一个空序列对象。
· void append(val) 将整数 val 添加在序列末尾。
· void addAll(inc) 将所有序列中的现有数值都增加 inc 。
· void multAll(m) 将序列中的所有现有数值都乘以整数 m 。
· int getIndex(idx) 得到下标为 idx 处的数值（下标从 0 开始），并将结果对 10^9 + 7 取余。如果下标大于等于序列的长度，请返回 -1 。



```
const MOD: i64 = 1_000_000_007;

/// 奇妙序列：支持追加、全体加、全体乘、按下标查询
///
/// 核心思想：延迟更新。维护每个元素的基础值 v[i]，以及全局的 a 和 b，
/// 使得元素的实际值 = v[i] * a_global + b_global（取模）。
///
/// 当执行 add_all(inc) 时，只需更新 b_global += inc
/// 当执行 mult_all(m) 时，只需更新 a_global *= m, b_global *= m
///
/// 为了处理 append 时新元素不受历史操作影响，需要记录每个元素被添加时的 a 和 b 快照。
struct Fancy {
    /// 每个元素的基础值
    vals: Vec<i64>,
    /// 每个元素添加时的全局乘数快照
    snap_a: Vec<i64>,
    /// 每个元素添加时的全局加数快照
    snap_b: Vec<i64>,
    /// 当前全局乘数
    cur_a: i64,
    /// 当前全局加数
    cur_b: i64,
}

impl Fancy {
    /// 初始化空序列
    pub fn new() -> Self {
        Self {
            vals: Vec::new(),
            snap_a: Vec::new(),
            snap_b: Vec::new(),
            cur_a: 1,
            cur_b: 0,
        }
    }

    /// 在序列末尾添加 val
    pub fn append(&mut self, val: i32) {
        self.vals.push(val as i64);
        // 记录当前全局状态，作为该元素的快照
        self.snap_a.push(self.cur_a);
        self.snap_b.push(self.cur_b);
    }

    /// 所有现有元素增加 inc
    pub fn add_all(&mut self, inc: i32) {
        self.cur_b = (self.cur_b + inc as i64) % MOD;
    }

    /// 所有现有元素乘以 m
    pub fn mult_all(&mut self, m: i32) {
        let m = m as i64;
        self.cur_a = (self.cur_a * m) % MOD;
        self.cur_b = (self.cur_b * m) % MOD;
    }

    /// 获取下标 idx 处的值，取模后返回。若 idx 越界返回 -1
    pub fn get_index(&self, idx: i32) -> i32 {
        let idx = idx as usize;
        if idx >= self.vals.len() {
            return -1;
        }

        // 实际值 = base_val * (cur_a / snap_a) + (cur_b - snap_b * cur_a / snap_a)
        // 即实际值 = (base_val - snap_b) * cur_a / snap_a + cur_b
        //
        // 更稳定的计算方式（避免负数）：
        // 实际值 = base_val * cur_a * inv(snap_a) + cur_b - snap_b * cur_a * inv(snap_a)
        //        = (base_val - snap_b) * cur_a * inv(snap_a) + cur_b
        let base = self.vals[idx];
        let snap_a = self.snap_a[idx];
        let snap_b = self.snap_b[idx];

        // 计算 factor = cur_a / snap_a mod MOD
        let factor = self.cur_a * mod_inv(snap_a) % MOD;

        // 实际值 = (base - snap_b) * factor + cur_b
        let val = ((base - snap_b + MOD) % MOD) * factor % MOD;
        let val = (val + self.cur_b) % MOD;

        val as i32
    }
}

/// 快速幂：计算 x^p mod MOD
#[inline]
fn mod_pow(mut x: i64, mut p: i64) -> i64 {
    let mut result = 1;
    while p > 0 {
        if p & 1 == 1 {
            result = result * x % MOD;
        }
        x = x * x % MOD;
        p >>= 1;
    }
    result
}

/// 乘法逆元：计算 x 在模 MOD 下的逆元 (MOD 为质数)
#[inline]
fn mod_inv(x: i64) -> i64 {
    mod_pow(x, MOD - 2)
}

```
