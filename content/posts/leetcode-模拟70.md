---
title: "leetcode-模拟70"
date: 2026-08-08T11:31:12+08:00
tags: ["leetcode", "模拟"]
draft: false
---


## 极大极小游戏

给你一个下标从 0 开始的整数数组 nums ，其长度是 2 的幂。

对 nums 执行下述算法：

1. 设 n 等于 nums 的长度，如果 n == 1 ，终止 算法过程。否则，创建 一个新的整数数组 newNums ，新数组长度为 n / 2 ，下标从 0 开始。
2. 对于满足 0 <= i < n / 2 的每个 偶数 下标 i ，将 newNums[i] 赋值 为 min(nums[2 * i], nums[2 * i + 1]) 。
3. 对于满足 0 <= i < n / 2 的每个 奇数 下标 i ，将 newNums[i] 赋值 为 max(nums[2 * i], nums[2 * i + 1]) 。
4. 用 newNums 替换 nums 。
5. 从步骤 1 开始 重复 整个过程。

执行算法后，返回 nums 中剩下的那个数字。


```
impl Solution {
    /// 模拟 min-max 游戏：每轮将数组两两配对，
    /// 偶数下标位置取最小值，奇数下标位置取最大值，直到只剩一个数。
    pub fn min_max_game(mut nums: Vec<i32>) -> i32 {
        let mut n = nums.len();
        while n > 1 {
            n /= 2;
            for i in 0..n {
                let (a, b) = (nums[2 * i], nums[2 * i + 1]);
                nums[i] = if i % 2 == 0 { a.min(b) } else { a.max(b) };
            }
        }
        nums[0]
    }
}
```
