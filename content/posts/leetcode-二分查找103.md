---
title: "leetcode-二分查找103"
date: 2026-09-28T12:29:17+08:00
tags: ["leetcode", "二分查找"]
draft: false
---


## 马戏团人塔

有个马戏团正在设计叠罗汉的表演节目，一个人要站在另一人的肩膀上。出于实际和美观的考虑，在上面的人要比下面的人矮一点且轻一点。已知马戏团每个人的身高和体重，请编写代码计算叠罗汉最多能叠几个人。


```
impl Solution {
    pub fn best_seq_at_index(height: Vec<i32>, weight: Vec<i32>) -> i32 {
        let mut pairs: Vec<(i32, i32)> = height.into_iter().zip(weight).collect();

        // 身高升序；身高相同时体重降序，避免同身高的人被同时选中
        pairs.sort_unstable_by(|a, b| a.0.cmp(&b.0).then(b.1.cmp(&a.1)));

        // 对体重求最长严格上升子序列（LIS）
        let mut dp: Vec<i32> = Vec::new();
        for (_, w) in pairs {
            match dp.binary_search(&w) {
                Ok(pos) => dp[pos] = w,
                Err(pos) => {
                    if pos == dp.len() {
                        dp.push(w);
                    } else {
                        dp[pos] = w;
                    }
                }
            }
        }

        dp.len() as i32
    }
}
```
