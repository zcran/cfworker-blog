---
title: "leetcode-前缀和93"
date: 2026-08-25T10:02:51+08:00
tags: ["leetcode", "前缀和"]
draft: false
---


## 回文串重新排列查询

给你一个长度为 偶数 n ，下标从 0 开始的字符串 s 。

同时给你一个下标从 0 开始的二维整数数组 queries ，其中 queries[i] = [ai, bi, ci, di] 。

对于每个查询 i ，你需要执行以下操作：

将下标在范围 0 <= ai <= bi < n / 2 内的 子字符串 s[ai:bi] 中的字符重新排列。
将下标在范围 n / 2 <= ci <= di < n 内的 子字符串 s[ci:di] 中的字符重新排列。

对于每个查询，你的任务是判断执行操作后能否让 s 变成一个 回文串 。

每个查询与其他查询都是 独立的 。

请你返回一个下标从 0 开始的数组 answer ，如果第 i 个查询执行操作后，可以将 s 变为一个回文串，那么 answer[i] = true，否则为 false 。

子字符串 指的是一个字符串中一段连续的字符序列。
s[x:y] 表示 s 中从下标 x 到 y 且两个端点 都包含 的子字符串。


```
impl Solution {
    pub fn can_make_palindrome_queries(s: String, queries: Vec<Vec<i32>>) -> Vec<bool> {
        let n = s.len() / 2;
        let s = s.as_bytes();
        let t: Vec<u8> = s[n..].iter().rev().copied().collect();

        // 前缀和：每个位置之前各字符出现次数
        let mut pre_s = vec![[0; 26]; n + 1];
        let mut pre_t = vec![[0; 26]; n + 1];
        for i in 0..n {
            pre_s[i + 1] = pre_s[i];
            pre_s[i + 1][(s[i] - b'a') as usize] += 1;
            pre_t[i + 1] = pre_t[i];
            pre_t[i + 1][(t[i] - b'a') as usize] += 1;
        }

        // diff[i] 表示 s[0..i) 中 s[j] != t[j] 的个数
        let mut diff = vec![0; n + 1];
        for i in 0..n {
            diff[i + 1] = diff[i] + (s[i] != t[i]) as i32;
        }

        // 获取区间 [l, r] 的字符计数（闭区间）
        let count = |pre: &[[i32; 26]], l: usize, r: usize| -> [i32; 26] {
            let mut res = pre[r + 1];
            for i in 0..26 {
                res[i] -= pre[l][i];
            }
            res
        };

        // 检查 s1 能否减去 s2（即 s1 每个字符都不少于 s2）
        let can_subtract = |s1: &[i32; 26], s2: &[i32; 26]| -> bool {
            s1.iter().zip(s2.iter()).all(|(&a, &b)| a >= b)
        };

        // 核心检查：第一个区间对应 pre_a，第二个对应 pre_b
        let check = |l1: usize, r1: usize, l2: usize, r2: usize,
                     pre_a: &[[i32; 26]], pre_b: &[[i32; 26]]| -> bool {
            // 区间外部必须已经匹配
            if diff[l1] > 0 || diff[n] - diff[r1.max(r2) + 1] > 0 {
                return false;
            }

            // 情况1：区间包含
            if r2 <= r1 {
                return count(pre_a, l1, r1) == count(pre_b, l1, r1);
            }

            // 情况2：区间不相交
            if r1 < l2 {
                return diff[l2] - diff[r1 + 1] == 0
                    && count(pre_a, l1, r1) == count(pre_b, l1, r1)
                    && count(pre_a, l2, r2) == count(pre_b, l2, r2);
            }

            // 情况3：区间相交但不包含
            // pre_a 多出的部分需要由 pre_b 补足
            let mut extra_a = count(pre_a, l1, r1);
            let part_b = count(pre_b, l1, l2 - 1);
            if !can_subtract(&extra_a, &part_b) {
                return false;
            }
            for i in 0..26 {
                extra_a[i] -= part_b[i];
            }

            // pre_b 多出的部分需要由 pre_a 补足
            let mut extra_b = count(pre_b, l2, r2);
            let part_a = count(pre_a, r1 + 1, r2);
            if !can_subtract(&extra_b, &part_a) {
                return false;
            }
            for i in 0..26 {
                extra_b[i] -= part_a[i];
            }

            extra_a == extra_b
        };

        queries
            .iter()
            .map(|q| {
                let (a, b, c, d) = (q[0] as usize, q[1] as usize, q[2] as usize, q[3] as usize);
                let l1 = a;
                let r1 = b;
                let l2 = 2 * n - 1 - d; // 右半映射到左半的配对起点
                let r2 = 2 * n - 1 - c; // 右半映射到左半的配对终点

                if l1 <= l2 {
                    // 第一个区间对应 s，第二个对应 t
                    check(l1, r1, l2, r2, &pre_s, &pre_t)
                } else {
                    // 交换区间并交换前缀和矩阵
                    check(l2, r2, l1, r1, &pre_t, &pre_s)
                }
            })
            .collect()
    }
}
```
