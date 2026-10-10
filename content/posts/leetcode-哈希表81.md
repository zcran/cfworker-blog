---
title: "leetcode-哈希表81"
date: 2026-10-09T20:08:01+08:00
tags: ["leetcode", "哈希表"]
draft: false
---


## 三个相等元素之间的最小距离 I

给你一个整数数组 nums。

如果满足 nums[i] == nums[j] == nums[k]，且 (i, j, k) 是 3 个 不同 下标，那么三元组 (i, j, k) 被称为 有效三元组 。

有效三元组 的 距离 被定义为 abs(i - j) + abs(j - k) + abs(k - i)，其中 abs(x) 表示 x 的 绝对值 。

返回一个整数，表示 有效三元组 的 最小 可能距离。如果不存在 有效三元组 ，返回 -1。


```
use std::collections::HashMap;

impl Solution {
    pub fn minimum_distance(nums: Vec<i32>) -> i32 {
        // 对每个值只保留最近两次出现的下标：(最近一次, 上一次)
        // 当第三次出现时，三个连续下标为 (second_last, last, idx)
        // 距离 = 2 * (idx - second_last)，取最小值即可
        let mut last_two: HashMap<i32, (usize, usize)> = HashMap::new();
        let mut best = usize::MAX;

        for (idx, &v) in nums.iter().enumerate() {
            let entry = last_two.entry(v).or_insert((usize::MAX, usize::MAX));
            let (last, second_last) = *entry;

            // 已存在至少两次出现，当前是第三次
            if second_last != usize::MAX {
                best = best.min(idx - second_last);
            }

            // 更新：最近一次变为当前 idx，上一次变为原来的 last
            *entry = (idx, last);
        }

        if best == usize::MAX {
            -1
        } else {
            (best * 2) as i32
        }
    }
}
```
