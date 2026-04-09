---
title: "leetcode-单调队列12"
date: 2026-03-31T20:55:29+08:00
tags: ["leetcode", "单调队列"]
draft: false
---


## 统计定界子数组的数目

给你一个整数数组 nums 和两个整数 minK 以及 maxK 。

nums 的定界子数组是满足下述条件的一个子数组：

子数组中的 最小值 等于 minK 。
子数组中的 最大值 等于 maxK 。
返回定界子数组的数目。

子数组是数组中的一个连续部分。

```
impl Solution {
    pub fn count_subarrays(nums: Vec<i32>, min_k: i32, max_k: i32) -> i64 {
        nums.iter()
            .enumerate()
            .fold(
                (-1i32, -1i32, -1i32, 0i64),
                |(last_out, last_min, last_max, res), (i, &num)| {
                    let i = i as i32;

                    if num < min_k || num > max_k {
                        (i, -1, -1, res)
                    } else {
                        let last_min = if num == min_k { i } else { last_min };
                        let last_max = if num == max_k { i } else { last_max };

                        let add = if last_min != -1 && last_max != -1 {
                            (last_min.min(last_max) - last_out) as i64
                        } else {
                            0
                        };

                        (last_out, last_min, last_max, res + add)
                    }
                }
            )
            .3
    }
}
```
