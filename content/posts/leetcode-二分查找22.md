---
title: "leetcode-二分查找22"
date: 2026-09-28T12:29:11+08:00
tags: ["leetcode", "二分查找"]
draft: false
---


## 到达终点数字

在一根无限长的数轴上，你站在0的位置。终点在target的位置。

你可以做一些数量的移动 numMoves :

每次你可以选择向左或向右移动。
第 i 次移动（从  i == 1 开始，到 i == numMoves ），在选择的方向上走 i 步。

给定整数 target ，返回 到达目标所需的 最小 移动次数(即最小 numMoves ) 。


```
impl Solution {
    /// 返回到达 target 所需的最小移动次数。
    ///
    /// 思路：第 i 步走 i，走 n 步总位移为 sum = n*(n+1)/2。
    /// 找到最小的 n 使 sum >= |target|。若 (sum - |target|) 为偶数，
    /// 则可通过翻转某一步方向抵消差值，n 即为答案；否则需增加步数调整奇偶性。
    pub fn reach_number(target: i32) -> i32 {
        let t = target.unsigned_abs() as u64;

        // 用求根公式估算最小 n，使 n*(n+1)/2 >= t
        let mut n = ((((8 * t + 1) as f64).sqrt() - 1.0) / 2.0).ceil() as u64;
        let mut sum = n * (n + 1) / 2;
        if sum < t {
            n += 1;
            sum += n;
        }

        // 若差值不是偶数，需要增加步数来翻转奇偶性
        if (sum - t) % 2 == 1 {
            // n 为偶数时，n+1 为奇数，加一步即可翻转奇偶性；
            // n 为奇数时，n+1 为偶数，需再加一步（共加 2 步）。
            n += if n % 2 == 0 { 1 } else { 2 };
        }

        n as i32
    }
}
```
