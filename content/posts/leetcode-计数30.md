---
title: "leetcode-计数30"
date: 2026-08-01T10:40:52+08:00
tags: ["leetcode", "计数"]
draft: false
---


## 好数对的数目

给你一个整数数组 nums 。

如果一组数字 (i,j) 满足 nums[i] == nums[j] 且 i < j ，就可以认为这是一组 好数对 。

返回好数对的数目。


```
impl Solution {
    pub fn num_identical_pairs(nums: Vec<i32>) -> i32 {
        use std::collections::HashMap;
        let mut count = HashMap::new();
        let mut pairs = 0;

        for num in nums {
            // 当前数字之前出现过的次数，就是可以和它组成的新好数对数量
            let prev = count.get(&num).unwrap_or(&0);
            pairs += prev;
            *count.entry(num).or_insert(0) += 1;
        }

        pairs
    }
}
```
