---
title: "leetcode-线段树11"
date: 2026-06-19T09:37:24+08:00
tags: ["leetcode", "线段树"]
draft: false
---


## 数组的最小稳定性因子

给你一个整数数组 nums 和一个整数 maxC。

如果一个 子数组 的所有元素的最大公因数（简称 HCF） 大于或等于 2，则称该子数组是稳定的。

一个数组的 稳定性因子 定义为其 最长 稳定子数组的长度。

你 最多 可以修改数组中的 maxC 个元素为任意整数。

在最多 maxC 次修改后，返回数组的 最小 可能稳定性因子。如果没有稳定的子数组，则返回 0。

注意:

· 子数组 是数组中连续的元素序列。
· 数组的 最大公因数（HCF）是能同时整除数组中所有元素的最大整数。
· 如果长度为 1 的 子数组 中唯一元素大于等于 2，那么它是稳定的，因为 HCF([x]) = x。


```
use std::collections::HashMap;

impl Solution {
    /// 计算在最多 maxC 次修改后，数组的最小可能稳定性因子
    ///
    /// 稳定性因子 = 最长稳定子数组的长度（稳定子数组的所有元素 HCF >= 2）
    ///
    /// 思路：二分答案 + 贪心验证
    /// 对于给定的长度 upper，检查是否能将所有长度 > upper 的稳定子数组破坏掉
    /// 使用 gcd 区间合并技巧，维护以当前右端点结尾的所有不同 gcd 值及其最小左端点
    pub fn min_stable(nums: Vec<i32>, max_c: i32) -> i32 {
        let n = nums.len();
        let max_c = max_c as usize;

        // 检查是否存在长度 > upper 的稳定子数组
        // 如果存在，需要用修改来破坏它
        let check = |upper: usize| -> bool {
            // intervals 存储 (gcd, 最小左端点)
            // 以当前右端点结尾的所有不同 gcd 值，按 gcd 从小到大排列
            let mut intervals: Vec<(i32, usize)> = Vec::new();
            let mut remaining = max_c;

            for i in 0..n {
                let x = nums[i];

                // 更新所有已有的 gcd 值
                for (g, _) in intervals.iter_mut() {
                    *g = gcd(*g, x);
                }

                // 当前元素单独作为一个子数组
                intervals.push((x, i));

                // 去重：合并相同的 gcd，保留最小的左端点
                let mut idx = 0;
                for j in 0..intervals.len() {
                    if idx == 0 || intervals[j].0 != intervals[idx - 1].0 {
                        intervals[idx] = intervals[j];
                        idx += 1;
                    }
                }
                intervals.truncate(idx);

                // 移除 gcd == 1 的区间（不稳定子数组）
                if !intervals.is_empty() && intervals[0].0 == 1 {
                    intervals.remove(0);
                }

                // 检查最长的稳定子数组是否超过 upper
                if !intervals.is_empty() {
                    let longest_len = i - intervals[0].1 + 1;
                    if longest_len > upper {
                        if remaining == 0 {
                            return false;
                        }
                        remaining -= 1;
                        // 修改一个元素后，所有以 i 结尾的子数组 gcd 都会变成 1
                        // 因此清空 intervals
                        intervals.clear();
                    }
                }
            }
            true
        };

        // 二分查找最小可能的稳定性因子
        // 下界：0（没有稳定子数组）
        // 上界：n / (max_c + 1)（每个修改最多能破坏 (max_c + 1) 个位置的稳定子数组）
        let mut left = 0;
        let mut right = n / (max_c + 1) + 1;

        while left < right {
            let mid = (left + right) / 2;
            if check(mid) {
                right = mid;
            } else {
                left = mid + 1;
            }
        }

        left as i32
    }
}

/// 计算两个数的最大公约数
#[inline]
fn gcd(mut a: i32, mut b: i32) -> i32 {
    while b != 0 {
        let temp = b;
        b = a % b;
        a = temp;
    }
    a.abs()
}
```
