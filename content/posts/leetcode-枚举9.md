---
title: "leetcode-枚举9"
date: 2026-07-09T10:04:59+08:00
tags: ["leetcode", "枚举"]
draft: false
---


## 重复至少 K 次且长度为 M 的模式


给你一个正整数数组 arr，请你找出一个长度为 m 且在数组中至少重复 k 次的模式。

模式 是由一个或多个值组成的子数组（连续的子序列），连续 重复多次但 不重叠 。 模式由其长度和重复次数定义。

如果数组中存在至少重复 k 次且长度为 m 的模式，则返回 true ，否则返回  false 。

```
impl Solution {
    /// 判断数组 arr 中是否存在长度为 m 的子数组，连续重复至少 k 次（不重叠）。
    pub fn contains_pattern(arr: Vec<i32>, m: i32, k: i32) -> bool {
        let m = m as usize;
        let k = k as usize;
        let total = m * k;

        // 长度不足，直接返回 false
        if arr.len() < total {
            return false;
        }

        // 滑动窗口检查每个可能的起始位置
        for start in 0..=arr.len() - total {
            let mut is_pattern = true;

            // 检查每个长度为 m 的块是否与前一个块相同
            for block in 1..k {
                let prev_start = start + (block - 1) * m;
                let curr_start = start + block * m;

                for offset in 0..m {
                    if arr[prev_start + offset] != arr[curr_start + offset] {
                        is_pattern = false;
                        break;
                    }
                }

                if !is_pattern {
                    break;
                }
            }

            if is_pattern {
                return true;
            }
        }

        false
    }
}
```



```
impl Solution {
    pub fn contains_pattern(arr: Vec<i32>, m: i32, k: i32) -> bool {
        arr.windows(m as usize * k as usize).any(|w| w.chunks(m as usize).all(|v| *v == w[0..m as usize]))
    }
}
```
