---
title: "leetcode-并查集1"
date: 2026-06-25T11:22:21+08:00
tags: ["leetcode", "并查集"]
draft: false
---


## 最长连续序列

给定一个未排序的整数数组 nums ，找出数字连续的最长序列（不要求序列元素在原数组中连续）的长度。

请你设计并实现时间复杂度为 O(n) 的算法解决此问题。


```
use std::collections::HashSet;

impl Solution {
    pub fn longest_consecutive(nums: Vec<i32>) -> i32 {
        // 去重并支持 O(1) 查找
        let set: HashSet<i32> = nums.into_iter().collect();
        let mut best = 0;

        for &num in &set {
            // 只从每个连续序列的最小值开始统计
            if set.contains(&(num - 1)) {
                continue;
            }

            // 从 num 开始向后延伸
            let mut cur = num;
            while set.contains(&(cur + 1)) {
                cur += 1;
            }

            // 更新最长长度
            best = best.max(cur - num + 1);
        }

        best
    }
}
```



```
impl Solution {
    pub fn longest_consecutive(mut nums: Vec<i32>) -> i32 {
        if nums.is_empty() {
            return 0;
        }

        nums.sort_unstable();

        let mut ans = 1;
        let mut cur = 1;

        for i in 1..nums.len() {
            if nums[i] == nums[i - 1] {
                continue;
            }

            if nums[i] == nums[i - 1] + 1 {
                cur += 1;
            } else {
                ans = ans.max(cur);
                cur = 1;
            }
        }

        ans.max(cur)
    }
}
```
