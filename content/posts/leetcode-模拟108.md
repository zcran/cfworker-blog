---
title: "leetcode-模拟108"
date: 2026-08-08T11:31:15+08:00
tags: ["leetcode", "模拟"]
draft: false
---


## 所有球里面不同颜色的数目

给你一个整数 limit 和一个大小为 n x 2 的二维数组 queries 。

总共有 limit + 1 个球，每个球的编号为 [0, limit] 中一个 互不相同 的数字。一开始，所有球都没有颜色。queries 中每次操作的格式为 [x, y] ，你需要将球 x 染上颜色 y 。每次操作之后，你需要求出所有球颜色的数目。

请你返回一个长度为 n 的数组 result ，其中 result[i] 是第 i 次操作以后颜色的数目。

注意 ，没有染色的球不算作一种颜色。


```
use std::collections::HashMap;

impl Solution {
 pub fn query_results(_limit: i32, queries: Vec<Vec<i32>>) -> Vec<i32> {
     let mut ans = Vec::with_capacity(queries.len());
     let mut ball_color = HashMap::new();  // 球 -> 当前颜色
     let mut color_cnt = HashMap::new();   // 颜色 -> 使用次数

     for q in &queries {
         let [x, y] = q.as_slice() else { continue };
         let x = *x;
         let y = *y;

         // 移除球 x 的旧颜色计数
         if let Some(&old) = ball_color.get(&x) {
             let count = color_cnt.get_mut(&old).unwrap();
             *count -= 1;
             if *count == 0 {
                 color_cnt.remove(&old);
             }
         }

         // 染上新的颜色
         ball_color.insert(x, y);
         *color_cnt.entry(y).or_insert(0) += 1;

         ans.push(color_cnt.len() as i32);
     }

     ans
 }
}
```
