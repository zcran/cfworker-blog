---
title: "leetcode-位运算80"
date: 2026-09-24T10:33:39+08:00
tags: ["leetcode", "位运算"]
draft: false
---


## 交替按位异或分割的数目

给你一个整数数组 nums 以及两个 互不相同 的整数 target1 和 target2。

nums 的一个 分割 是指将其划分为一个或多个 连续且非空 的块，这些块在不重叠的情况下覆盖整个数组。

如果一个分割中各块元素的 按位异或 结果在 target1 和 target2 之间 交替 出现，且以 target1 开始，则称该分割是 有效的。

形式上，对于块 b1, b2, ... ：

XOR(b1) = target1
XOR(b2) = target2（如果存在）
XOR(b3) = target1，以此类推。

返回 nums 的有效分割方案数，结果对 109 + 7 取余。

注意： 如果单个块的 按位异或 结果等于 target1，则该分割也是有效的。


```
impl Solution {
    pub fn alternating_xor(nums: Vec<i32>, target1: i32, target2: i32) -> i32 {
        // 动态规划 + 前缀异或 + 哈希表
        //
        // f1[s]：前缀异或为 s，且最后一段异或值为 target1 的方案数
        // f2[s]：前缀异或为 s，且最后一段异或值为 target2 的方案数
        //
        // 当前前缀 pre，若以 target1 结尾，则之前一段必须以 target2 结尾，
        // 方案数取自 f2[pre ^ target1]；反之取自 f1[pre ^ target2]。
        //
        // 初始：f2[0] = 1，表示"空前缀视为以 target2 结尾"，使首段可取 target1。

        const MOD: i64 = 1_000_000_007;
        use std::collections::HashMap;

        let mut f1: HashMap<i32, i64> = HashMap::new();
        let mut f2: HashMap<i32, i64> = HashMap::new();
        f2.insert(0, 1);

        let mut pre = 0i32;
        let last = nums.len() - 1;

        for (i, &x) in nums.iter().enumerate() {
            pre ^= x;

            let last1 = f2.get(&(pre ^ target1)).copied().unwrap_or(0);
            let last2 = f1.get(&(pre ^ target2)).copied().unwrap_or(0);

            if i == last {
                return ((last1 + last2) % MOD) as i32;
            }

            // 累加到 f1[pre] / f2[pre]（使用 entry 一次性完成读改写）
            let e1 = f1.entry(pre).or_insert(0);
            *e1 = (*e1 + last1) % MOD;
            let e2 = f2.entry(pre).or_insert(0);
            *e2 = (*e2 + last2) % MOD;
        }

        0
    }
}
```
