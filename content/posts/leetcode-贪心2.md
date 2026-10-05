---
title: "leetcode-贪心2"
date: 2026-10-03T12:55:34+08:00
tags: ["leetcode", "贪心"]
draft: false
---


## 跳跃游戏 II

给定一个长度为 n 的 0 索引整数数组 nums。初始位置在下标 0。

每个元素 nums[i] 表示从索引 i 向后跳转的最大长度。换句话说，如果你在索引 i 处，你可以跳转到任意 (i + j) 处：

0 <= j <= nums[i] 且

i + j < n

返回到达 n - 1 的最小跳跃次数。测试用例保证可以到达 n - 1。


```
impl Solution {
    pub fn jump(nums: Vec<i32>) -> i32 {
        // 贪心：在每一跳能到达的范围内，选择能跳到最远的下一跳
        let (mut jumps, mut cur_end, mut farthest) = (0, 0, 0);

        // 只需遍历到倒数第二个元素，因为到达最后一个元素时无需再跳
        for i in 0..nums.len() - 1 {
            farthest = farthest.max(i + nums[i] as usize);
            // 到达当前跳跃范围的边界，必须再跳一次
            if i == cur_end {
                jumps += 1;
                cur_end = farthest;
            }
        }

        jumps
    }
}
```
