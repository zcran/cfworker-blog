---
title: "leetcode-模拟47"
date: 2026-08-08T11:31:11+08:00
tags: ["leetcode", "模拟"]
draft: false
---


## 找到需要补充粉笔的学生编号

一个班级里有 n 个学生，编号为 0 到 n - 1 。每个学生会依次回答问题，编号为 0 的学生先回答，然后是编号为 1 的学生，以此类推，直到编号为 n - 1 的学生，然后老师会重复这个过程，重新从编号为 0 的学生开始回答问题。

给你一个长度为 n 且下标从 0 开始的整数数组 chalk 和一个整数 k 。一开始粉笔盒里总共有 k 支粉笔。当编号为 i 的学生回答问题时，他会消耗 chalk[i] 支粉笔。如果剩余粉笔数量 严格小于 chalk[i] ，那么学生 i 需要 补充 粉笔。

请你返回需要 补充 粉笔的学生 编号 。


```
impl Solution {
    pub fn chalk_replacer(chalk: Vec<i32>, k: i32) -> i32 {
        let sum: u64 = chalk.iter().map(|&c| c as u64).sum();
        let mut rem = k as u64 % sum;  // 最后一轮剩余的粉笔

        for (i, &c) in chalk.iter().enumerate() {
            if c as u64 > rem { return i as i32; }
            rem -= c as u64;
        }
        0
    }
}
```
