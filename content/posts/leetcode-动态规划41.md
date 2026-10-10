---
title: "leetcode-动态规划41"
date: 2026-10-05T20:39:56+08:00
tags: ["leetcode", "动态规划"]
draft: false
---


## 删除并获得点数

给你一个整数数组 nums ，你可以对它进行一些操作。

每次操作中，选择任意一个 nums[i] ，删除它并获得 nums[i] 的点数。之后，你必须删除 所有 等于 nums[i] - 1 和 nums[i] + 1 的元素。

开始你拥有 0 个点数。返回你能通过这些操作获得的最大点数。




```
impl Solution {
    /// 198. 打家劫舍：不能偷相邻房屋，求最大收益
    fn rob(nums: &[i32]) -> i32 {
        // f0: 不选当前房屋时的最大收益，f1: 选当前房屋时的最大收益
        let mut f0 = 0;
        let mut f1 = 0;
        for &x in nums {
            let new_f1 = f1.max(f0 + x);
            f0 = f1;
            f1 = new_f1;
        }
        f1
    }

    /// 740. 删除并获得点数
    pub fn delete_and_earn(nums: Vec<i32>) -> i32 {
        let mx = nums.iter().copied().max().unwrap_or(0) as usize;
        // sum[v] 表示所有等于 v 的元素之和
        let mut sum = vec![0; mx + 1];
        for x in nums {
            sum[x as usize] += x;
        }
        // 转化为打家劫舍：选择了 v 就不能选择 v-1 和 v+1
        Self::rob(&sum)
    }
}
```
