---
title: "leetcode-贪心130"
date: 2026-10-03T12:55:42+08:00
tags: ["leetcode", "贪心"]
draft: false
---


## 合法分组的最少组数

给你一组带编号的 balls 并要求将它们分类到盒子里，以便均衡地分配。你必须遵守两条规则：

同一个盒子里的球必须具有相同的编号。但是，如果你有多个相同编号的球，你可以把它们放在不同的盒子里。

最大的盒子只能比最小的盒子多一个球。

返回遵循上述规则排列这些球所需要的盒子的最小数目。


```
use std::collections::HashMap;

impl Solution {
    pub fn min_groups_for_valid_assignment(nums: Vec<i32>) -> i32 {
        // 1. 统计每个编号出现的次数
        let mut cnt = HashMap::new();
        for num in nums {
            *cnt.entry(num).or_insert(0) += 1;
        }
        let counts: Vec<i32> = cnt.into_values().collect();

        // 2. 设所有盒子的容量为 c 或 c+1。
        //    最小容量 c 不会超过出现次数最少的编号（否则该编号无法装盒）。
        let min_count = *counts.iter().min().unwrap();
        let mut best = i32::MAX;

        // 3. 枚举 c，对每个编号求所需最少盒数，累加取最小
        for c in 1..=min_count {
            let mut total = 0;
            let mut ok = true;
            for &num in &counts {
                // 设用 k 个盒子，其中 x 个装 c+1 个、k-x 个装 c 个：
                //   c*k + x = num,  0 <= x <= k
                // 由此 k 必须满足 ceil(num/(c+1)) <= k <= floor(num/c)。
                // 取最小可行的 k 即最少盒数。
                let k = (num + c) / (c + 1); // ceil(num / (c+1))
                if k * c > num {
                    ok = false;
                    break;
                }
                total += k;
            }
            if ok {
                best = best.min(total);
            }
        }

        if best == i32::MAX { -1 } else { best }
    }
}
```
