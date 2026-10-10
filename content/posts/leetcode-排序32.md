---
title: "leetcode-排序32"
date: 2026-10-05T00:35:01+08:00
tags: ["leetcode", "排序"]
draft: false
---


## 安排邮筒

给你一个房屋数组houses 和一个整数 k ，其中 houses[i] 是第 i 栋房子在一条街上的位置，现需要在这条街上安排 k 个邮筒。

请你返回每栋房子与离它最近的邮筒之间的距离的 最小 总和。

答案保证在 32 位有符号整数范围以内。


```
impl Solution {
    /// 在街上安排 k 个邮筒，使每栋房子到最近邮筒的距离总和最小
    ///
    /// 思路：
    ///   1. 房子位置排序后，一段连续房子 [i, j] 共用一个邮筒时的最小代价，
    ///      等于它们到中位数的距离之和，即 medsum[i][j]；
    ///      利用「两端成对」递推：medsum[i][j] = medsum[i+1][j-1] + houses[j] - houses[i]。
    ///   2. 区间 DP：f[i][j] 表示前 i+1 栋房子安排 j 个邮筒的最小代价，
    ///      转移：f[i][j] = min over i0 < i { f[i0][j-1] + medsum[i0+1][i] }。
    /// 时间 O(n² · k)，额外空间 O(n²)。
    pub fn min_distance(houses: Vec<i32>, k: i32) -> i32 {
        let n = houses.len();
        let k = k as usize;
        let mut houses = houses;
        houses.sort_unstable();

        // medsum[i][j]：houses[i..=j] 共用一个邮筒的最小距离和
        let mut medsum = vec![vec![0i32; n]; n];
        for i in (0..n).rev() {
            for j in i + 1..n {
                // 两端成对向中位数靠拢，递推复用内层结果
                medsum[i][j] = medsum[i + 1][j - 1] + houses[j] - houses[i];
            }
        }

        // f[i][j]：前 i+1 栋房子安排 j 个邮筒的最小代价
        let mut f = vec![vec![i32::MAX / 2; k + 1]; n];
        for i in 0..n {
            f[i][1] = medsum[0][i];
            // 邮筒数不超过房子数
            for j in 2..=k.min(i + 1) {
                for i0 in 0..i {
                    let cost = f[i0][j - 1] + medsum[i0 + 1][i];
                    if cost < f[i][j] {
                        f[i][j] = cost;
                    }
                }
            }
        }

        f[n - 1][k]
    }
}
```
