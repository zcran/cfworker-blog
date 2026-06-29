---
title: "leetcode-数论60"
date: 2026-06-20T11:09:57+08:00
tags: ["leetcode", "数论"]
draft: false
---


## K 因数分解

给你两个整数 n 和 k，将数字 n 恰好分割成 k 个正整数，使得这些整数的 乘积 等于 n。

返回一个分割方案，使得这些数字中 最大值 和 最小值 之间的 差值 最小化。结果可以以 任意顺序 返回。


```
const MX: usize = 100_001;

lazy_static::lazy_static! {
    /// 预处理每个数的所有因子（从小到大排序）
    static ref DIVISORS: Vec<Vec<usize>> = {
        let mut divs = vec![vec![]; MX];
        for i in 1..MX {
            for j in (i..MX).step_by(i) {
                divs[j].push(i);
            }
        }
        divs
    };
}

impl Solution {
    /// 将 n 分割成 k 个正整数，使最大值与最小值的差值最小
    pub fn min_difference(n: i32, k: i32) -> Vec<i32> {
        let n = n as usize;
        let k = k as usize;

        let mut best_diff = i32::MAX;
        let mut best_path = vec![0; k];
        let mut current_path = vec![0; k];

        // DFS: 当前处理第 i 个位置，剩余乘积 rem，当前最小值和最大值
        fn dfs(
            i: usize,
            rem: usize,
            cur_min: i32,
            cur_max: i32,
            k: usize,
            n: usize,
            best_diff: &mut i32,
            best_path: &mut Vec<i32>,
            current_path: &mut Vec<i32>,
            divisors: &Vec<Vec<usize>>,
        ) {
            if i == k - 1 {
                // 最后一个数就是 rem
                let last = rem as i32;
                let diff = cur_max.max(last) - cur_min.min(last);
                if diff < *best_diff {
                    *best_diff = diff;
                    current_path[i] = last;
                    *best_path = current_path.clone();
                }
                return;
            }

            // 枚举当前数的所有因子
            for &d in &divisors[rem] {
                // 剪枝：确保剩余因子能分配到后续位置
                let remaining = rem / d;
                if remaining < 1 { continue; }

                current_path[i] = d as i32;
                dfs(
                    i + 1,
                    remaining,
                    cur_min.min(d as i32),
                    cur_max.max(d as i32),
                    k,
                    n,
                    best_diff,
                    best_path,
                    current_path,
                    divisors,
                );
            }
        }

        dfs(0, n, i32::MAX, 0, k, n, &mut best_diff, &mut best_path, &mut current_path, &DIVISORS);
        best_path
    }
}
```
