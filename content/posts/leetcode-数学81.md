---
title: "leetcode-数学81"
date: 2026-10-07T20:39:33+08:00
tags: ["leetcode", "数学"]
draft: false
---


## 镜像对之间最小绝对距离

给你一个整数数组 nums。

镜像对 是指一对满足下述条件的下标 (i, j)：

0 <= i < j < nums.length，并且

reverse(nums[i]) == nums[j]，其中 reverse(x) 表示将整数 x 的数字反转后形成的整数。反转后会忽略前导零，例如 reverse(120) = 21。

返回任意镜像对的下标之间的 最小绝对距离。下标 i 和 j 之间的绝对距离为 abs(i - j)。

如果不存在镜像对，返回 -1。


```
use std::collections::HashMap;

impl Solution {
    pub fn min_mirror_pair_distance(nums: Vec<i32>) -> i32 {
        // 反转整数并去掉前导零
        fn reverse(mut x: i32) -> i32 {
            let mut y = 0;
            while x > 0 {
                y = y * 10 + x % 10;
                x /= 10;
            }
            y
        }

        // 哈希表记录某个"反转后的值"上次出现的下标。
        // 遍历时，若当前值 x 之前作为某个数的反转出现过，
        // 则当前下标与那个下标的距离即为一个候选答案。
        let mut last_seen: HashMap<i32, usize> = HashMap::new();
        let mut ans = usize::MAX;

        for (i, &x) in nums.iter().enumerate() {
            if let Some(&j) = last_seen.get(&x) {
                ans = ans.min(i - j);
            }
            last_seen.insert(reverse(x), i);
        }

        if ans == usize::MAX { -1 } else { ans as i32 }
    }
}
```
