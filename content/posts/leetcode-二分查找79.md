---
title: "leetcode-二分查找79"
date: 2026-09-28T12:29:15+08:00
tags: ["leetcode", "二分查找"]
draft: false
---


## 最大化游戏分数的最小值

给你一个长度为 n 的数组 points 和一个整数 m 。同时有另外一个长度为 n 的数组 gameScore ，其中 gameScore[i] 表示第 i 个游戏得到的分数。一开始对于所有的 i 都有 gameScore[i] == 0 。

你开始于下标 -1 处，该下标在数组以外（在下标 0 前面一个位置）。你可以执行 至多 m 次操作，每一次操作中，你可以执行以下两个操作之一：

将下标增加 1 ，同时将 points[i] 添加到 gameScore[i] 。
将下标减少 1 ，同时将 points[i] 添加到 gameScore[i] 。

注意，在第一次移动以后，下标必须始终保持在数组范围以内。

请你返回 至多 m 次操作以后，gameScore 里面最小值 最大 为多少。


```
impl Solution {
    pub fn max_score(points: Vec<i32>, m: i32) -> i64 {
        let n = points.len();
        let m = m as i64;

        // 判断能否让 gameScore 中每个元素都至少达到 low
        let can_achieve = |low: i64| -> bool {
            let mut rem = m;
            let mut pre = 0_i64; // 上一个位置顺带被操作的次数
            for i in 0..n {
                // 当前下标还需要操作的次数
                let mut k = (low - 1) / points[i] as i64 + 1 - pre;
                if i == n - 1 && k <= 0 {
                    // 最后一个位置已满足，无需再移动
                    break;
                }
                k = k.max(1); // 至少要走一步到达该位置
                rem -= k * 2 - 1; // 来回横跳所需的操作次数
                if rem < 0 {
                    return false;
                }
                pre = k - 1; // 移动到下一位置时，顺带操作了下一位置 k-1 次
            }
            true
        };

        // 二分答案：上界为 (m+1)/2 次全部作用在最小 points 上
        let min_points = *points.iter().min().unwrap() as i64;
        let mut left = 0;
        let mut right = (m + 1) / 2 * min_points + 1;
        while left + 1 < right {
            let mid = left + (right - left) / 2;
            if can_achieve(mid) {
                left = mid;
            } else {
                right = mid;
            }
        }
        left
    }
}
```
