---
title: "leetcode-位掩码11"
date: 2026-05-18T10:27:59+08:00
tags: ["leetcode", "位掩码"]
draft: false
---


## 并行课程 II


给你一个整数 n 表示某所大学里课程的数目，编号为 1 到 n ，数组 relations 中， relations[i] = [xi, yi]  表示一个先修课的关系，也就是课程 xi 必须在课程 yi 之前上。同时你还有一个整数 k 。

在一个学期中，你 最多 可以同时上 k 门课，前提是这些课的先修课在之前的学期里已经上过了。

请你返回上完所有课最少需要多少个学期。题目保证一定存在一种上完所有课的方式。


 ```
 impl Solution {
     pub fn min_number_of_semesters(n: i32, relations: Vec<Vec<i32>>, k: i32) -> i32 {
         let n = n as usize;
         let k = k as usize;
         let total = 1 << n;

         // prereq[i] : 课程 i 的所有先修课程位掩码
         let mut prereq = vec![0usize; n];
         for rel in relations {
             let a = (rel[0] - 1) as usize;
             let b = (rel[1] - 1) as usize;
             prereq[b] |= 1 << a;
         }

         // preq[mask] : mask 中所有课程的先修课程并集
         let mut preq = vec![0usize; total];
         let mut valid = vec![false; total];

         for mask in 0..total {
             let cnt = mask.count_ones() as usize;
             if cnt <= k {
                 let mut m = mask;
                 while m != 0 {
                     let i = m.trailing_zeros() as usize;
                     preq[mask] |= prereq[i];
                     m &= m - 1;
                 }
                 if preq[mask] & mask == 0 {
                     valid[mask] = true;
                 }
             }
         }

         let mut dp = vec![i32::MAX; total];
         dp[0] = 0;

         for mask in 0..total {
             if dp[mask] == i32::MAX {
                 continue;
             }
             let mut can_take: usize = 0;
             for i in 0..n {
                 if (mask >> i) & 1 == 0 && (prereq[i] & mask) == prereq[i] {
                     can_take |= 1 << i;
                 }
             }
             let mut sub = can_take;
             while sub != 0 {
                 if sub.count_ones() as usize <= k && valid[sub] {
                     let next = mask | sub;
                     dp[next] = dp[next].min(dp[mask] + 1);
                 }
                 sub = (sub - 1) & can_take;
             }
         }

         dp[total - 1]
     }
 }
 ```
