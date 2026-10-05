---
title: "leetcode-贪心41"
date: 2026-10-03T12:55:36+08:00
tags: ["leetcode", "贪心"]
draft: false
---


## 可被三整除的最大和

给你一个整数数组 nums，请你找出并返回能被三整除的元素 最大和。


```
impl Solution {
    pub fn max_sum_div_three(nums: Vec<i32>) -> i32 {
        // 贪心：先取全部元素的和 total
        // 若 total % 3 == 0 直接返回
        // 否则需要移除最小代价的元素，使和变为 3 的倍数：
        //   total % 3 == 1：移除 1 个 mod1 元素，或 2 个 mod2 元素
        //   total % 3 == 2：移除 1 个 mod2 元素，或 2 个 mod1 元素
        let mut total = 0;
        let (mut mod1, mut mod2) = (Vec::new(), Vec::new());

        for num in nums {
            total += num;
            match num % 3 {
                1 => mod1.push(num),
                2 => mod2.push(num),
                _ => {}
            }
        }

        match total % 3 {
            0 => total,
            r => {
                mod1.sort_unstable();
                mod2.sort_unstable();

                // 计算最小移除代价
                let mut remove = i32::MAX;
                if r == 1 {
                    if !mod1.is_empty() {
                        remove = remove.min(mod1[0]);
                    }
                    if mod2.len() >= 2 {
                        remove = remove.min(mod2[0] + mod2[1]);
                    }
                } else {
                    if !mod2.is_empty() {
                        remove = remove.min(mod2[0]);
                    }
                    if mod1.len() >= 2 {
                        remove = remove.min(mod1[0] + mod1[1]);
                    }
                }

                if remove == i32::MAX { 0 } else { total - remove }
            }
        }
    }
}
```
