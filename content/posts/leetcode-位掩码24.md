---
title: "leetcode-位掩码24"
date: 2026-05-18T10:27:59+08:00
tags: ["leetcode", "位掩码"]
draft: false
---


##  将数组分成两个数组并最小化数组和的差


给你一个长度为 2 * n 的整数数组。你需要将 nums 分成 两个 长度为 n 的数组，分别求出两个数组的和，并 最小化 两个数组和之 差的绝对值 。nums 中每个元素都需要放入两个数组之一。

请你返回 最小 的数组和之差。

```
impl Solution {
    pub fn minimum_difference(mut nums: Vec<i32>) -> i32 {
        let n = nums.len() / 2;
        let total_sum: i32 = nums.iter_mut().map(|x| { *x *= 2; *x }).sum();
        let half_sum = total_sum / 2;

        let (left, right) = nums.split_at(n);

        let left_sums = Self::subset_sums_by_size_fp(left);
        let right_sums = Self::subset_sums_by_size_fp(right);

        (0..=n)
            .map(|i| Self::closest_sum_fp(&left_sums[i], &right_sums[n - i], half_sum))
            .min()
            .unwrap()
    }

    fn subset_sums_by_size_fp(arr: &[i32]) -> Vec<Vec<i32>> {
        let n = arr.len();
        let total = 1 << n;
        // 使用 fold 构建 sums 向量
        let sums = (1..total).fold(vec![0; total], |mut sums, mask| {
            let lsb = mask & (mask as i32).wrapping_neg() as usize;
            let idx = lsb.trailing_zeros() as usize;
            sums[mask] = sums[mask ^ lsb] + arr[idx];
            sums
        });
        // 分组：利用 enumerate 和 for_each
        let mut groups = (0..=n).map(|_| Vec::new()).collect::<Vec<_>>();
        (0..total).for_each(|mask| {
            let size = mask.count_ones() as usize;
            groups[size].push(sums[mask]);
        });
        groups.iter_mut().for_each(|v| v.sort_unstable());
        groups
    }

    fn closest_sum_fp(a: &[i32], b: &[i32], target: i32) -> i32 {
        a.iter()
            .flat_map(|&x| {
                let need = target - x;
                match b.binary_search(&need) {
                    Ok(_) => vec![0],
                    Err(pos) => {
                        let mut diffs = Vec::with_capacity(2);
                        if pos < b.len() {
                            diffs.push((need - b[pos]).abs());
                        }
                        if pos > 0 {
                            diffs.push((need - b[pos - 1]).abs());
                        }
                        diffs
                    }
                }
            })
            .min()
            .unwrap_or(i32::MAX)
    }
}
```
