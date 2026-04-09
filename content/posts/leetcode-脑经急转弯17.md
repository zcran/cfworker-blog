---
title: "leetcode-脑经急转弯17"
date: 2026-03-24T20:55:57+08:00
tags: ["leetcode", "脑筋急转弯"]
draft: false
---


## 删除子数组后的最终元素

给你一个整数数组 nums。

有两名玩家，Alice 和 Bob，轮流进行游戏，Alice 先手。

在每一轮中，当前玩家可以选择任意一个子数组 nums[l..r]，满足 r - l + 1 < m，其中 m 是 当前数组的长度。
被选中的 子数组将被移除，剩余的元素将连接 起来形成新的数组。
游戏持续进行，直到 仅剩一个 元素为止。
Alice 的目标是 最大化 最终剩下的元素，而 Bob 的目标则是 最小化 它。假设双方都采取最优策略，返回最终剩下的元素的值。

子数组 是数组中连续的且 非空 的一段元素。


```
impl Solution {
    pub fn final_element(nums: Vec<i32>) -> i32 {
        nums[0].max(*nums.last().unwrap())
    }
}
```
