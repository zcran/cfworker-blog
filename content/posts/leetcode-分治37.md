---
title: "leetcode-分治37"
date: 2026-06-13T10:51:39+08:00
tags: ["leetcode", "分治"]
draft: false
---


## 递增后的数对数量

给你两个整数数组 nums1 和 nums2，以及一个二维整数数组 queries。

每个 queries[i] 都属于以下两种类型之一：

· [1, x, y, val]：将 nums2[x..y] 中的每个元素都 增加 val。
· [2, tot]：计算 满足 nums1[j] + nums2[k] == tot 的数对 (j, k) 的数量。

返回一个整数数组 answer，其中 answer[j] 表示第 jth 个类型 2 查询的数对数量。

```
use std::collections::HashMap;
use std::cmp::min;

impl Solution {
    pub fn number_of_pairs(
        nums1: Vec<i32>,
        nums2: Vec<i32>,
        queries: Vec<Vec<i32>>,
    ) -> Vec<i32> {
        let n = nums2.len();
        let m = nums1.len();
        let block_size = ((m * n) as f64).sqrt() as usize;
        let block_size = block_size.max(1);

        // 块数据结构
        struct Block {
            left: usize,
            right: usize,
            freq: HashMap<i32, i32>,
            add: i32,
        }

        let mut blocks = Vec::new();
        let mut start = 0;
        while start < n {
            let end = min(start + block_size, n);
            let mut freq = HashMap::new();
            for &val in &nums2[start..end] {
                *freq.entry(val).or_insert(0) += 1;
            }
            blocks.push(Block {
                left: start,
                right: end,
                freq,
                add: 0,
            });
            start = end;
        }

        let mut nums2 = nums2;
        let mut result = Vec::new();
        let mx = 1_000_000_001;

        for query in queries {
            match query[0] {
                1 => {
                    let l = query[1] as usize;
                    let r = query[2] as usize + 1;
                    let val = query[3];

                    for block in &mut blocks {
                        if block.right <= l || block.left >= r {
                            continue;
                        }

                        if l <= block.left && block.right <= r {
                            block.add += val;
                            continue;
                        }

                        let update_left = block.left.max(l);
                        let update_right = block.right.min(r);

                        for idx in update_left..update_right {
                            let old_val = nums2[idx];
                            let new_val = min(old_val + val, mx);

                            // 更新频率
                            if let Some(count) = block.freq.get_mut(&old_val) {
                                *count -= 1;
                                if *count == 0 {
                                    block.freq.remove(&old_val);
                                }
                            }
                            *block.freq.entry(new_val).or_insert(0) += 1;

                            nums2[idx] = new_val;
                        }
                    }
                }

                2 => {
                    let target = query[1];
                    let mut total = 0;

                    for block in &blocks {
                        let adjusted = target - block.add;
                        // 使用迭代器高效计算
                        total += nums1.iter()
                            .filter_map(|&x| block.freq.get(&(adjusted - x)))
                            .sum::<i32>();
                    }

                    result.push(total);
                }

                _ => unreachable!(),
            }
        }

        result
    }
}
```
