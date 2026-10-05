---
title: "leetcode-贪心3"
date: 2026-10-03T12:55:34+08:00
tags: ["leetcode", "贪心"]
draft: false
---


## 跳跃游戏

给你一个非负整数数组 nums ，你最初位于数组的 第一个下标 。数组中的每个元素代表你在该位置可以跳跃的最大长度。

判断你是否能够到达最后一个下标，如果可以，返回 true ；否则，返回 false 。


```
impl Solution {
    pub fn can_jump(nums: Vec<i32>) -> bool {
        // 贪心：维护当前能到达的最远下标
        let last = nums.len() - 1;
        let mut max_reach = 0;

        for (i, &jump) in nums.iter().enumerate() {
            // i 已超出可达范围，无法继续
            if i > max_reach {
                return false;
            }
            max_reach = max_reach.max(i + jump as usize);
            // 已能到达末尾，无需继续遍历
            if max_reach >= last {
                return true;
            }
        }

        true
    }
}
```
