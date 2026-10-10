---
title: "leetcode-动态规划59"
date: 2026-10-05T20:39:57+08:00
tags: ["leetcode", "动态规划"]
draft: false
---


## 至少有 1 位重复的数字

给定正整数 n，返回在 [1, n] 范围内具有 至少 1 位 重复数字的正整数的个数。


```
impl Solution {
    /// 1012. 至少有 1 位重复数字
    ///
    /// 用补集：答案 = n - (各位互不相同的正整数个数)。
    ///
    /// 数位 DP 计算 [1, n] 中各位互不相同的数的个数：
    ///  - 先加上所有位数少于 len 的数（前缀表 pre_sum 直接给出）。
    ///  - 再从高位到低位逐位枚举，对每一位选一个比当前位小、且未用过的数字，
    ///    剩余低位可任意排列（从剩余数字中取 k 个的排列数 = P(10 - index - 1, k)）。
    ///  - 最后若 n 本身各位也互不相同，则把它本身也计入。
    ///
    /// pre_sum[k] = 位数为 1..=k 的"各位互不相同"的正整数个数之和。
    pub fn num_dup_digits_at_most_n(n: i32) -> i32 {
        // pre_sum[k]：长度 1..=k 的各位互不相同的正整数个数之和
        const PRE_SUM: [i32; 10] = [0, 9, 90, 738, 5274, 32490, 168570, 712890, 2345850, 5611770];

        let digits: Vec<i32> = n.to_string().bytes().map(|b| (b - b'0') as i32).collect();
        let len = digits.len();

        // 排列数 P(f, k) = f * (f-1) * ... * (f-k+1)
        let perm = |mut f: i32, k: i32| -> i32 {
            let mut res = 1;
            for _ in 0..k {
                res *= f;
                f -= 1;
            }
            res
        };

        // 位数不足 len 的部分
        let mut distinct = PRE_SUM[len - 1];
        let mut used = [false; 10];

        // 逐位枚举，统计与 n 位数相同、字典序小于 n 且各位互不相同的数
        for (i, &d) in digits.iter().enumerate() {
            // 首位不能为 0
            let start = if i == 0 { 1 } else { 0 };
            for x in start..d {
                if !used[x as usize] {
                    // 剩余 len - i - 1 位从剩余 (10 - i - 1) 个数字中任取排列
                    distinct += perm(10 - i as i32 - 1, len as i32 - i as i32 - 1);
                }
            }
            // 若当前位已在前面出现过，则 n 本身不满足"各位互不相同"，无需再枚举
            if used[d as usize] {
                return n - distinct;
            }
            used[d as usize] = true;
        }

        // n 本身各位也互不相同，计入
        distinct + 1;
        n - (distinct + 1)
    }
}
```
