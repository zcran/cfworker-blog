---
title: "leetcode-位掩码20"
date: 2026-05-18T10:27:59+08:00
tags: ["leetcode", "位掩码"]
draft: false
---


## 最大兼容性评分和

有一份由 n 个问题组成的调查问卷，每个问题的答案要么是 0（no，否），要么是 1（yes，是）。

这份调查问卷被分发给 m 名学生和 m 名导师，学生和导师的编号都是从 0 到 m - 1 。学生的答案用一个二维整数数组 students 表示，其中 students[i] 是一个整数数组，包含第 i 名学生对调查问卷给出的答案（下标从 0 开始）。导师的答案用一个二维整数数组 mentors 表示，其中 mentors[j] 是一个整数数组，包含第 j 名导师对调查问卷给出的答案（下标从 0 开始）。

每个学生都会被分配给 一名 导师，而每位导师也会分配到 一名 学生。配对的学生与导师之间的兼容性评分等于学生和导师答案相同的次数。

例如，学生答案为[1, 0, 1] 而导师答案为 [0, 0, 1] ，那么他们的兼容性评分为 2 ，因为只有第二个和第三个答案相同。

请你找出最优的学生与导师的配对方案，以 最大程度上 提高 兼容性评分和 。

给你 students 和 mentors ，返回可以得到的 最大兼容性评分和 。


```
impl Solution {
    pub fn max_compatibility_sum(students: Vec<Vec<i32>>, mentors: Vec<Vec<i32>>) -> i32 {
        let n = students.len();
        // 预计算所有学生与所有导师的兼容分数
        let mut score = vec![vec![0; n]; n];
        for i in 0..n {
            for j in 0..n {
                let compat = students[i].iter().zip(&mentors[j])
                    .filter(|&(a, b)| a == b)
                    .count() as i32;
                score[i][j] = compat;
            }
        }

        let full = 1 << n;
        let mut dp = vec![0; full];
        // dp[mask] 表示已分配导师集合为 mask 时的最大总分
        for mask in 0..full {
            let i = mask.count_ones() as usize; // 下一个待分配的学生索引
            if i == n { continue; }
            // 尝试给第 i 个学生分配一个未使用的导师 j
            for j in 0..n {
                if (mask >> j) & 1 == 0 {
                    let next = mask | (1 << j);
                    let cand = dp[mask] + score[i][j];
                    if cand > dp[next] {
                        dp[next] = cand;
                    }
                }
            }
        }
        dp[full - 1]
    }
}
```
