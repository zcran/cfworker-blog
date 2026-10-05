---
title: "leetcode-前缀和146"
date: 2026-08-25T10:02:55+08:00
tags: ["leetcode", "前缀和"]
draft: false
---


## 按奇偶比统计子数组 II

给你一个整数数组 nums，以及两个整数 a 和 b。

对于一个 子数组 ，定义：

· x 表示其中偶数元素的数量。
· y 表示其中奇数元素的数量。

子数组中偶数与奇数的比例定义为 x / y，其中该比例按照精确的有理数值进行比较。

如果一个子数组满足以下条件，则称其为 有效子数组 ：

· y > 0，并且
· x / y <= a / b。

返回 nums 中有效子数组的数量。

子数组 是数组中一个连续的 非空 元素序列。


```
impl Solution {
    pub fn count_ratio_subarrays(nums: Vec<i32>, a: i32, b: i32) -> i64 {
        let n = nums.len();
        let a = a as i64;
        let b = b as i64;

        // 构建前缀和：偶数贡献 -b，奇数贡献 a
        // 条件 x/y <= a/b  =>  x*b <= a*y  =>  a*y - b*x >= 0
        // 将每个奇数视为 a，偶数视为 -b，前缀和即为 a*y - b*x
        let mut prefix = vec![0i64; n + 1];
        for i in 0..n {
            let val = if nums[i] & 1 == 1 { a } else { -b };
            prefix[i + 1] = prefix[i] + val;
        }

        // 离散化所有前缀和
        let mut sorted = prefix.clone();
        sorted.sort_unstable();
        sorted.dedup();

        // 树状数组（Fenwick Tree）：统计已遍历的前缀和数量
        let m = sorted.len();
        let mut bit = vec![0i32; m + 1];

        // 定义更新和查询函数（使用引用传递 bit）
        let mut update = |bit: &mut Vec<i32>, mut idx: usize| {
            while idx <= m {
                bit[idx] += 1;
                idx += idx & (!idx + 1);
            }
        };

        let mut query = |bit: &Vec<i32>, mut idx: usize| -> i64 {
            let mut res = 0;
            while idx > 0 {
                res += bit[idx] as i64;
                idx -= idx & (!idx + 1);
            }
            res
        };

        let mut ans = 0i64;
        for &s in &prefix {
            // 找到 s 在离散化数组中的位置（1-based）
            let idx = sorted.partition_point(|&x| x < s) + 1;
            // 查询之前有多少个前缀和 <= s（这些子数组满足条件）
            ans += query(&bit, idx);
            // 将当前前缀和加入树状数组
            update(&mut bit, idx);
        }

        ans
    }
}
```
