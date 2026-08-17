---
title: "leetcode-模拟62"
date: 2026-08-08T11:31:12+08:00
tags: ["leetcode", "模拟"]
draft: false
---


## 将找到的值乘以 2

给你一个整数数组 nums ，另给你一个整数 original ，这是需要在 nums 中搜索的第一个数字。

接下来，你需要按下述步骤操作：

1. 如果在 nums 中找到 original ，将 original 乘以 2 ，得到新 original（即，令 original = 2 * original）。
2. 否则，停止这一过程。
3. 只要能在数组中找到新 original ，就对新 original 继续 重复 这一过程。

返回 original 的 最终 值。


```
impl Solution {
    /// 不断将 original 乘以 2，只要结果存在于 nums 中就继续，返回最终值。
    pub fn find_final_value(nums: Vec<i32>, mut original: i32) -> i32 {
        let set: std::collections::HashSet<i32> = nums.into_iter().collect();
        while set.contains(&original) {
            original *= 2;
        }
        original
    }
}
```
