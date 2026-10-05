---
title: "leetcode-贪心69"
date: 2026-10-03T12:55:38+08:00
tags: ["leetcode", "贪心"]
draft: false
---


## 构成特定和需要添加的最少元素

给你一个整数数组 nums ，和两个整数 limit 与 goal 。数组 nums 有一条重要属性：abs(nums[i]) <= limit 。

返回使数组元素总和等于 goal 所需要向数组中添加的 最少元素数量 ，添加元素 不应改变 数组中 abs(nums[i]) <= limit 这一属性。

注意，如果 x >= 0 ，那么 abs(x) 等于 x ；否则，等于 -x 。


```
impl Solution {
    pub fn min_elements(nums: Vec<i32>, limit: i32, goal: i32) -> i32 {
        // 当前总和与目标的差值（用 i64 防止溢出）
        let diff = (goal as i64 - nums.iter().map(|&x| x as i64).sum::<i64>()).abs();
        let limit = limit as i64;
        // 每个新元素最多贡献 limit，向上取整即为最少个数
        ((diff + limit - 1) / limit) as i32
    }
}
```
