---
title: "Rust Note 09"
date: 2023-05-19T21:35:49+08:00
tags: ["rust", "leetcode", "初级算法篇"]
draft: false
---
## 两数之和
给定一个整数数组 nums 和一个整数目标值 target，请你在该数组中找出 和为目标值 target  的那 两个 整数，并返回它们的数组下标。

你可以假设每种输入只会对应一个答案。但是，数组中同一个元素在答案里不能重复出现。

你可以按任意顺序返回答案。


### C 解法：
```
int* twoSum(int* nums, int numsSize, int target, int* returnSize) {
    for (int i = 0; i < numsSize; ++i) {
        for (int j = i + 1; j < numsSize; ++j) {
            if (nums[i] + nums[j] == target) {
                int* ret = malloc(sizeof(int) * 2);
                ret[0] = i, ret[1] = j;
                *returnSize = 2;
                return ret;
            }
        }
    }
    *returnSize = 0;
    return NULL;
}
```


### Rust 解法：
```
use std::collections::HashMap;

impl Solution {
    pub fn two_sum(nums: Vec<i32>, target: i32) -> Vec<i32> {
       let mut map = HashMap::new();
        for (i,val) in nums.iter().enumerate() {
            if let Some(val) = map.get(&(target - val)) {
                return vec![*val as i32, i as i32];
            } else {
                map.insert(val, i);
            }
        }
        vec![]
    }
}
```