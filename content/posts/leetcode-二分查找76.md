---
title: "leetcode-二分查找76"
date: 2026-09-28T12:29:15+08:00
tags: ["leetcode", "二分查找"]
draft: false
---


## 最小化相邻元素的最大差值

给你一个整数数组 nums 。nums 中的一些值 缺失 了，缺失的元素标记为 -1 。

你需要选择 一个正 整数数对 (x, y) ，并将 nums 中每一个 缺失 元素用 x 或者 y 替换。

你的任务是替换 nums 中的所有缺失元素，最小化 替换后数组中相邻元素 绝对差值 的 最大值 。

请你返回上述要求下的 最小值 。




```
impl Solution {
    pub fn min_difference(nums: Vec<i32>) -> i32 {
        let n = nums.len();

        // 找出与 -1 相邻的非缺失元素中的最小值和最大值
        let mut min_adj = i32::MAX;
        let mut max_adj = i32::MIN;
        for i in 0..n {
            if nums[i] != -1
                && ((i > 0 && nums[i - 1] == -1) || (i + 1 < n && nums[i + 1] == -1))
            {
                min_adj = min_adj.min(nums[i]);
                max_adj = max_adj.max(nums[i]);
            }
        }

        // 计算两个固定值之间夹着若干 -1 时的最小化最大相邻差
        // left/right 是区间两端的固定值，big 表示中间 -1 的个数 >= 2
        let calc = |left: i32, right: i32, big: bool| -> i32 {
            let mut dist = ((right - min_adj).min(max_adj - left) + 1) / 2;
            if big {
                dist = dist.min((max_adj - min_adj + 2) / 3);
            }
            dist
        };

        let mut ans = 0;
        let mut prev: Option<usize> = None;

        for (i, &num) in nums.iter().enumerate() {
            if num == -1 {
                continue;
            }

            match prev {
                Some(p) if i - p == 1 => {
                    // 相邻两个固定值，直接取差
                    ans = ans.max(num.abs_diff(nums[p]) as i32);
                }
                Some(p) => {
                    // 两个固定值之间夹着 -1
                    ans = ans.max(calc(
                        nums[p].min(num),
                        nums[p].max(num),
                        i - p > 2,
                    ));
                }
                None if i > 0 => {
                    // 数组开头就是 -1，只与右侧固定值有关
                    ans = ans.max(calc(num, num, false));
                }
                None => {}
            }

            prev = Some(i);
        }

        // 数组末尾是 -1 的情况
        if let Some(p) = prev {
            if p < n - 1 {
                ans = ans.max(calc(nums[p], nums[p], false));
            }
        }

        ans
    }
}
```
