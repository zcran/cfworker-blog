---
title: "leetcode-计数12"
date: 2026-08-01T10:40:51+08:00
tags: ["leetcode", "计数"]
draft: false
---


## 使数组唯一的最小增量

给你一个整数数组 nums 。每次 move 操作将会选择任意一个满足 0 <= i < nums.length 的下标 i，并将 nums[i] 递增 1。

返回使 nums 中的每个值都变成唯一的所需要的最少操作次数。

生成的测试用例保证答案在 32 位整数范围内。


```
impl Solution {
    pub fn min_increment_for_unique(mut nums: Vec<i32>) -> i32 {
        // 先排序，使重复元素相邻，方便处理
        nums.sort_unstable();

        let mut moves = 0; // 记录需要增加的总次数

        // 从第二个元素开始，确保每个元素都严格大于前一个
        for i in 1..nums.len() {
            let prev = nums[i - 1];
            let curr = nums[i];

            // 当前元素需要变成的最小合法值：前一个元素 + 1
            let target = prev + 1;

            // 如果当前元素小于目标值，需要增加
            if curr < target {
                moves += target - curr; // 累加需要增加的步数
                nums[i] = target;       // 更新当前元素为合法值
            }
            // 如果 curr >= target，则无需修改，继续保持
        }

        moves
    }
}
```
