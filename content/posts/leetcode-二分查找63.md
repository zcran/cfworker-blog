---
title: "leetcode-二分查找63"
date: 2026-09-28T12:29:14+08:00
tags: ["leetcode", "二分查找"]
draft: false
---


## 打家劫舍 IV

沿街有一排连续的房屋。每间房屋内都藏有一定的现金。现在有一位小偷计划从这些房屋中窃取现金。

由于相邻的房屋装有相互连通的防盗系统，所以小偷 不会窃取相邻的房屋 。

小偷的 窃取能力 定义为他在窃取过程中能从单间房屋中窃取的 最大金额 。

给你一个整数数组 nums 表示每间房屋存放的现金金额。形式上，从左起第 i 间房屋中放有 nums[i] 美元。

另给你一个整数 k ，表示窃贼将会窃取的 最少 房屋数。小偷总能窃取至少 k 间房屋。

返回小偷的 最小 窃取能力。


```
impl Solution {
    pub fn min_capability(nums: Vec<i32>, k: i32) -> i32 {
        // 二分答案：能力值越大，越能偷到至少 k 间房屋
        let can_rob = |cap: i32| -> bool {
            let mut cnt = 0;
            let mut i = 0;
            while i < nums.len() {
                if nums[i] <= cap {
                    cnt += 1;
                    i += 2; // 偷了这间，跳过下一间
                } else {
                    i += 1;
                }
            }
            cnt >= k
        };

        let mut left = 1;
        let mut right = *nums.iter().max().unwrap() + 1;
        while left < right {
            let mid = left + (right - left) / 2;
            if can_rob(mid) {
                right = mid;
            } else {
                left = mid + 1;
            }
        }
        left
    }
}
```
