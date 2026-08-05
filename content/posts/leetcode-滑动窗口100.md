---
title: "leetcode-滑动窗口100"
date: 2026-07-18T11:02:35+08:00
tags: ["leetcode", "滑动窗口"]
draft: false
---


## 计算子数组的 x-sum I

给你一个由 n 个整数组成的数组 nums，以及两个整数 k 和 x。

数组的 x-sum 计算按照以下步骤进行：

统计数组中所有元素的出现次数。
仅保留出现频率最高的前 x 种元素。如果两种元素的出现次数相同，则数值 较大 的元素被认为出现次数更多。
计算结果数组的和。
注意，如果数组中的不同元素少于 x 个，则其 x-sum 是数组的元素总和。

返回一个长度为 n - k + 1 的整数数组 answer，其中 answer[i] 是 子数组 nums[i..i + k - 1] 的 x-sum。

子数组 是数组内的一个连续 非空 的元素序列。


```
use std::collections::HashMap;

impl Solution {
    pub fn find_x_sum(nums: Vec<i32>, k: i32, x: i32) -> Vec<i32> {
        let n = nums.len();
        let k = k as usize;
        let x = x as usize;
        let mut ans = vec![0; n - k + 1];

        // 统计当前窗口内元素频率
        let mut freq: HashMap<i32, usize> = HashMap::with_capacity(k);

        for i in 0..n {
            // 1. 添加右端元素到窗口
            *freq.entry(nums[i]).or_insert(0) += 1;

            // 窗口未满，继续扩展
            if i < k - 1 {
                continue;
            }

            // 2. 移除左端元素（窗口已满后）
            if i >= k {
                let left_val = nums[i - k];
                match freq.get_mut(&left_val) {
                    Some(count) => {
                        *count -= 1;
                        if *count == 0 {
                            freq.remove(&left_val);
                        }
                    }
                    None => unreachable!(),
                }
            }

            // 3. 计算当前窗口的 x-sum
            let window_start = i - k + 1;

            // 如果不同元素少于 x 个，直接求和
            if freq.len() <= x {
                ans[window_start] = freq.iter().map(|(&val, &cnt)| val * cnt as i32).sum();
                continue;
            }

            // 否则，按频率降序、值降序排序，取前 x 个
            let mut entries: Vec<_> = freq.iter().collect();
            entries.sort_unstable_by(|a, b| {
                match b.1.cmp(a.1) {
                    std::cmp::Ordering::Equal => b.0.cmp(a.0), // 频率相同，值大的优先
                    other => other,
                }
            });

            ans[window_start] = entries[..x]
                .iter()
                .map(|entry| {
                    let (val, cnt) = *entry;
                    val * (*cnt as i32)
                })
                .sum();
        }

        ans
    }
}
```
