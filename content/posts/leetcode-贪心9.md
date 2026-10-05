---
title: "leetcode-贪心9"
date: 2026-10-03T12:55:34+08:00
tags: ["leetcode", "贪心"]
draft: false
---


## 递增的三元子序列

给你一个整数数组 nums ，判断这个数组中是否存在长度为 3 的递增子序列。

如果存在这样的三元组下标 (i, j, k) 且满足 i < j < k ，使得 nums[i] < nums[j] < nums[k] ，返回 true ；否则，返回 false 。


```
impl Solution {
    pub fn increasing_triplet(nums: Vec<i32>) -> bool {
        // first: 目前找到的最小值（长度为 1 的递增子序列结尾）
        // second: 目前找到的、能构成递增二元组的最小结尾值
        let (mut first, mut second) = (i32::MAX, i32::MAX);

        for x in nums {
            if x > second {
                // 找到比 second 更大的值，构成长度为 3 的递增子序列
                return true;
            } else if x > first {
                // 更新长度为 2 的递增子序列的结尾
                second = x;
            } else {
                // 更新长度为 1 的递增子序列的结尾
                first = x;
            }
        }

        false
    }
}
```
