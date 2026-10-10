---
title: "leetcode-动态规划23"
date: 2026-10-05T20:39:55+08:00
tags: ["leetcode", "动态规划"]
draft: false
---


## 分割等和子集


给你一个 只包含正整数 的 非空 数组 nums 。请你判断是否可以将这个数组分割成两个子集，使得两个子集的元素和相等。



```
impl Solution {
    pub fn can_partition(nums: Vec<i32>) -> bool {
        let total: i32 = nums.iter().sum();
        // 总和为奇数，无法等分
        if total % 2 != 0 {
            return false;
        }
        let target = (total / 2) as usize;

        // f[j]：能否用已处理的元素凑出和 j
        let mut f = vec![false; target + 1];
        f[0] = true;

        // reach 记录当前已能达到的最大和，缩小内层循环范围
        let mut reach = 0usize;
        for x in nums {
            let x = x as usize;
            reach = (reach + x).min(target);
            // 逆序更新，保证每个元素只被使用一次
            for j in (x..=reach).rev() {
                f[j] = f[j] || f[j - x];
            }
            if f[target] {
                return true;
            }
        }

        false
    }
}
```
