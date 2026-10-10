---
title: "leetcode-数组64"
date: 2026-10-10T20:38:44+08:00
tags: ["leetcode", "数组"]
draft: false
---


## 统计下标的相反奇偶性得分

给你一个长度为 n 的整数数组 nums。

下标 i 的 分数 定义为满足以下条件的下标 j 的数量：

i < j < n，并且
nums[i] 和 nums[j] 的奇偶性不同（一个为偶数，另一个为奇数）。

返回一个长度为 n 的整数数组 answer，其中 answer[i] 表示下标 i 的分数。


```
impl Solution {
    pub fn count_opposite_parity(nums: Vec<i32>) -> Vec<i32> {
        let n = nums.len();
        let mut ans = vec![0; n];
        let mut odd_right = 0; // 右侧（i 之后）奇数的个数

        // 从右往左遍历，维护右侧奇数计数
        for i in (0..n).rev() {
            ans[i] = if nums[i] & 1 == 1 {
                // 当前是奇数，需要右侧偶数个数 = 右侧总数 - 右侧奇数个数
                (n - i - 1) as i32 - odd_right
            } else {
                // 当前是偶数，需要右侧奇数个数
                odd_right
            };

            if nums[i] & 1 == 1 {
                odd_right += 1;
            }
        }

        ans
    }
}
```
