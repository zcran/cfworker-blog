---
title: "leetcode-排序75"
date: 2026-10-05T00:35:04+08:00
tags: ["leetcode", "排序"]
draft: false
---


## 数位和排序需要的最小交换次数

给你一个由 互不相同 的正整数组成的数组 nums，需要根据每个数字的数位和（即每一位数字相加求和）按 升序 对数组进行排序。如果两个数字的数位和相等，则较小的数字排在前面。

返回将 nums 排列为上述排序顺序所需的 最小 交换次数。

一次 交换 定义为交换数组中两个不同位置的值。


```
impl Solution {
    pub fn min_swaps(nums: Vec<i32>) -> i32 {
        let n = nums.len();

        // 计算每个元素的数位和，并记录其原始下标
        let mut items: Vec<(i32, i32, usize)> = nums
            .iter()
            .enumerate()
            .map(|(i, &v)| {
                let mut sum = 0;
                let mut x = v;
                while x > 0 {
                    sum += x % 10;
                    x /= 10;
                }
                (sum, v, i) // (数位和, 数值, 原始下标)
            })
            .collect();

        // 按 (数位和, 数值) 升序排序，得到目标排列
        items.sort_unstable();

        // 并查集：统计置换环，每个大小为 s 的环需要 s-1 次交换
        let mut parent: Vec<usize> = (0..n).collect();
        let mut components = n;

        fn find(parent: &mut Vec<usize>, x: usize) -> usize {
            if parent[x] != x {
                parent[x] = find(parent, parent[x]);
            }
            parent[x]
        }

        for (target_pos, &(_, _, origin)) in items.iter().enumerate() {
            let rx = find(&mut parent, target_pos);
            let ry = find(&mut parent, origin);
            if rx != ry {
                parent[rx] = ry;
                components -= 1;
            }
        }

        (n - components) as i32
    }
}
```
