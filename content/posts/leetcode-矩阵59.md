---
title: "leetcode-矩阵59"
date: 2026-09-19T10:41:36+08:00
tags: ["leetcode", "矩阵"]
draft: false
---


## 找出缺失和重复的数字

给你一个下标从 0 开始的二维整数矩阵 grid，大小为 n * n ，其中的值在 [1, n2] 范围内。除了 a 出现 两次，b 缺失 之外，每个整数都 恰好出现一次 。

任务是找出重复的数字a 和缺失的数字 b 。

返回一个下标从 0 开始、长度为 2 的整数数组 ans ，其中 ans[0] 等于 a ，ans[1] 等于 b 。


```
impl Solution {
    /// 值域为 1..=n²，用计数数组统计每个数出现次数：
    ///   出现 2 次的是重复值 a，出现 0 次的是缺失值 b。
    /// 时间复杂度 O(n²)，空间 O(n²)。
    pub fn find_missing_and_repeated_values(grid: Vec<Vec<i32>>) -> Vec<i32> {
        let n = grid.len();
        let mut count = vec![0u8; n * n + 1];

        for row in &grid {
            for &val in row {
                count[val as usize] += 1;
            }
        }

        // 一次遍历同时找 a 和 b，找到即可提前结束
        let mut a = 0;
        let mut b = 0;
        for i in 1..=n * n {
            match count[i] {
                2 => a = i as i32,
                0 => b = i as i32,
                _ => {}
            }
            if a != 0 && b != 0 {
                break;
            }
        }

        vec![a, b]
    }
}
```
