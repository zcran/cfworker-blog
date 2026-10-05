---
title: "leetcode-二分查找45"
date: 2026-09-28T12:29:13+08:00
tags: ["leetcode", "二分查找"]
draft: false
---


## 袋子里最少数目的球

给你一个整数数组 nums ，其中 nums[i] 表示第 i 个袋子里球的数目。同时给你一个整数 maxOperations 。

你可以进行如下操作至多 maxOperations 次：

选择任意一个袋子，并将袋子里的球分到 2 个新的袋子中，每个袋子里都有 正整数 个球。
    ·比方说，一个袋子里有 5 个球，你可以把它们分到两个新袋子里，分别有 1 个和 4 个球，或者分别有 2 个和 3 个球。

你的开销是单个袋子里球数目的 最大值 ，你想要 最小化 开销。

请你返回进行上述操作后的最小开销。


```
impl Solution {
    pub fn minimum_size(nums: Vec<i32>, max_operations: i32) -> i32 {
        // 判断能否在 max_operations 次操作内，使所有袋子球数不超过 m
        let check = |m: i32| -> bool {
            let mut ops = 0i64;
            for &x in &nums {
                // 把 x 分成每份不超过 m，最少需要 (x - 1) / m 次操作
                ops += ((x - 1) / m) as i64;
                if ops > max_operations as i64 {
                    return false;
                }
            }
            true
        };

        // 二分答案：left 一定不可行，right 一定可行
        let mut left = 0;
        let mut right = *nums.iter().max().unwrap();
        while left + 1 < right {
            let mid = left + (right - left) / 2;
            if check(mid) {
                right = mid;
            } else {
                left = mid;
            }
        }
        right
    }
}
```
