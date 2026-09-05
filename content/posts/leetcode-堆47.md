---
title: "leetcode-堆47"
date: 2026-08-21T09:54:13+08:00
tags: ["leetcode", "堆"]
draft: false
---


## 数位和相等数对的最大和

给你一个下标从 0 开始的数组 nums ，数组中的元素都是 正 整数。请你选出两个下标 i 和 j（i != j），且 nums[i] 的数位和 与  nums[j] 的数位和相等。

请你找出所有满足条件的下标 i 和 j ，找出并返回 nums[i] + nums[j] 可以得到的 最大值。如果不存在这样的下标对，返回 -1。




```
impl Solution {
    pub fn maximum_sum(mut nums: Vec<i32>) -> i32 {
        let mut best = [0; 82];
        let mut ans = -1;

        for num in nums {
            let mut s = 0;
            let mut x = num;
            while x > 0 {
                s += (x % 10) as usize;
                x /= 10;
            }
            if best[s] > 0 {
                ans = ans.max(best[s] + num);
            }
            best[s] = best[s].max(num);
        }
        ans
    }
}
```
