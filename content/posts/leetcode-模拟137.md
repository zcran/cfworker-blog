---
title: "leetcode-模拟137"
date: 2026-08-08T11:31:17+08:00
tags: ["leetcode", "模拟"]
draft: false
---


## 计算比赛分数差

给你一个整数数组 nums，其中 nums[i] 表示在第 i 场比赛中获得的分数。

恰好 有两位玩家。初始时，第一位玩家为 主动玩家，第二位玩家为 被动玩家。

按顺序 将下述规则应用于每场比赛 i：

如果 nums[i] 是奇数，主动玩家和被动玩家互换角色。
在每第 6 场比赛（即比赛索引为 5, 11, 17, ... 的比赛中），主动玩家和被动玩家互换角色。
主动玩家参与第 i 场比赛，并获得 nums[i] 分。

返回 分数差，即第一位玩家的 总分 减去第二位玩家的 总分 。


```
impl Solution {
    /// 计算两位玩家的分数差
    ///
    /// 优化点：
    /// - 单变量 `diff` 维护分数差，替代 score[2] 数组
    /// - 位运算 `& 1` 替代 `% 2`
    /// - `iter().enumerate()` 替代索引访问，零边界检查
    pub fn score_difference(nums: Vec<i32>) -> i32 {
        let mut diff = 0i32;
        let mut active = 0i32; // 0=第一位玩家主动, 1=第二位玩家主动

        for (i, &x) in nums.iter().enumerate() {
            active ^= x & 1;               // 奇数分数切换主动玩家
            active ^= (i % 6 == 5) as i32; // 每第6场比赛切换主动玩家
            diff += if active == 0 { x } else { -x };
        }

        diff
    }
}
```
