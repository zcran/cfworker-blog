---
title: "leetcode-位运算90"
date: 2026-09-24T10:33:40+08:00
tags: ["leetcode", "位运算"]
draft: false
---


## 训练计划 VI

教学过程中，教练示范一次，学员跟做三次。该过程被混乱剪辑后，记录于数组 actions，其中 actions[i] 表示做出该动作的人员编号。请返回教练的编号。


```
impl Solution {
    pub fn training_plan(nums: Vec<i32>) -> i32 {
        // 教练出现 1 次，学员各出现 3 次。
        // 用状态机（one, two）实现"每个二进制位上 1 的计数 mod 3"：
        //   one：该位当前计数 mod 3 == 1
        //   two：该位当前计数 mod 3 == 2
        let mut one = 0;
        let mut two = 0;
        for &n in &nums {
            // 先更新 one（用旧的 two），再更新 two（用新的 one）
            one = (one ^ n) & !two;
            two = (two ^ n) & !one;
        }
        // 计数 mod 3 == 1 的位组成只出现一次的数，即教练编号
        one
    }
}
```
