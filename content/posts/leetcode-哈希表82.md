---
title: "leetcode-哈希表82"
date: 2026-10-09T20:08:01+08:00
tags: ["leetcode", "哈希表"]
draft: false
---


## 三个相等元素之间的最小距离 II

给你一个整数数组 nums。

如果满足 nums[i] == nums[j] == nums[k]，且 (i, j, k) 是 3 个 不同 下标，那么三元组 (i, j, k) 被称为 有效三元组 。

有效三元组 的 距离 被定义为 abs(i - j) + abs(j - k) + abs(k - i)，其中 abs(x) 表示 x 的 绝对值 。

返回一个整数，表示 有效三元组 的 最小 可能距离。如果不存在 有效三元组 ，返回 -1。


```
use std::collections::HashMap;

impl Solution {
    pub fn minimum_distance(nums: Vec<i32>) -> i32 {
        // 对同一值的三个下标 i < j < k，距离 = (j-i)+(k-j)+(k-i) = 2*(k-i)。
        // 所以只需对每个值，找"三个连续出现位置"中跨度最小的那一组。
        // 用 HashMap 记录每个值已出现的最近两个下标即可。
        let mut last_two: HashMap<i32, (usize, usize)> = HashMap::new(); // (最近, 次近)
        let mut best = usize::MAX;

        for (idx, &v) in nums.iter().enumerate() {
            let entry = last_two.entry(v).or_insert((usize::MAX, usize::MAX));
            let (last, second_last) = *entry;

            // 当前是第三次及以后的出现，能与前两次构成三元组
            if second_last != usize::MAX {
                best = best.min(idx - second_last);
            }

            // 更新最近两次出现位置
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
