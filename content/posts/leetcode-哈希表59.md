---
title: "leetcode-哈希表59"
date: 2026-10-09T20:07:59+08:00
tags: ["leetcode", "哈希表"]
draft: false
---


## 转换二维数组

给你一个整数数组 nums 。请你创建一个满足以下条件的二维数组：

二维数组应该 只 包含数组 nums 中的元素。
二维数组中的每一行都包含 不同 的整数。
二维数组的行数应尽可能 少 。

返回结果数组。如果存在多种答案，则返回其中任何一种。

请注意，二维数组的每一行上可以存在不同数量的元素。


```
use std::collections::HashMap;

impl Solution {
    pub fn find_matrix(nums: Vec<i32>) -> Vec<Vec<i32>> {
        // 统计每个数字出现的次数，次数即该数字需要占用的行数
        let mut cnt: HashMap<i32, i32> = HashMap::new();
        for x in nums {
            *cnt.entry(x).or_insert(0) += 1;
        }

        let mut ans: Vec<Vec<i32>> = Vec::new();

        // 每个数字在第 0..count 行各出现一次，即可保证每行不重复
        for (x, count) in cnt {
            for i in 0..count as usize {
                if i == ans.len() {
                    ans.push(Vec::new());
                }
                ans[i].push(x);
            }
        }

        ans
    }
}
```
