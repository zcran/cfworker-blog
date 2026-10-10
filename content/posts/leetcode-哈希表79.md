---
title: "leetcode-哈希表79"
date: 2026-10-09T20:08:00+08:00
tags: ["leetcode", "哈希表"]
draft: false
---


## 大于平均值的最小未出现正整数

给你一个整数数组 nums。

返回 nums 中 严格大于 nums 中所有元素 平均值 的 最小未出现正整数。

数组的 平均值 定义为数组中所有元素的总和除以元素的数量。


```
use std::collections::HashSet;

impl Solution {
    pub fn smallest_absent(nums: Vec<i32>) -> i32 {
        // 记录已出现的值，便于判断候选是否缺失
        let present: HashSet<i32> = nums.iter().copied().collect();

        // 平均值 = 总和 / 元素个数（整数除法，向零取整）
        let avg = nums.iter().sum::<i32>() / nums.len() as i32;

        // 答案必须是严格大于平均值的正整数
        let mut ans = avg.max(0) + 1;
        while present.contains(&ans) {
            ans += 1;
        }
        ans
    }
}
```
