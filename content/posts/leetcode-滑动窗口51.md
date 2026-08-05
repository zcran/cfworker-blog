---
title: "leetcode-滑动窗口51"
date: 2026-07-18T11:02:32+08:00
tags: ["leetcode", "滑动窗口"]
draft: false
---


## 摘水果

在一个无限的 x 坐标轴上，有许多水果分布在其中某些位置。给你一个二维整数数组 fruits ，其中 fruits[i] = [positioni, amounti] 表示共有 amounti 个水果放置在 positioni 上。fruits 已经按 positioni 升序排列 ，每个 positioni 互不相同 。

另给你两个整数 startPos 和 k 。最初，你位于 startPos 。从任何位置，你可以选择 向左或者向右 走。在 x 轴上每移动 一个单位 ，就记作 一步 。你总共可以走 最多 k 步。你每达到一个位置，都会摘掉全部的水果，水果也将从该位置消失（不会再生）。

返回你可以摘到水果的 最大总数 。


```
impl Solution {
    pub fn max_total_fruits(fruits: Vec<Vec<i32>>, start_pos: i32, k: i32) -> i32 {
        let n = fruits.len();
        let mut max_sum = 0;
        let mut window_sum = 0;
        let mut left = 0;

        // 滑动窗口：遍历右边界
        for right in 0..n {
            let pos_right = fruits[right][0];
            window_sum += fruits[right][1];

            // 如果当前窗口 [left, right] 无法在 k 步内从 start_pos 到达，则缩小左边界
            while left <= right {
                let pos_left = fruits[left][0];

                // 计算覆盖 [pos_left, pos_right] 所需的最少步数
                // 策略：先走到较近的一端，再走到较远的一端
                let dist_to_left = (pos_left - start_pos).abs();
                let dist_to_right = (pos_right - start_pos).abs();
                let min_steps = (pos_right - pos_left) + dist_to_left.min(dist_to_right);

                if min_steps <= k {
                    break;
                }

                // 无法覆盖，移动左边界
                window_sum -= fruits[left][1];
                left += 1;
            }

            max_sum = max_sum.max(window_sum);
        }

        max_sum
    }
}
```
