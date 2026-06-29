---
title: "leetcode-数论36"
date: 2026-06-20T11:09:56+08:00
tags: ["leetcode", "数论"]
draft: false
---


## 美丽下标对的数目

给你一个下标从 0 开始的整数数组 nums 。如果下标对 i、j 满足 0 ≤ i < j < nums.length ，如果 nums[i] 的 第一个数字 和 nums[j] 的 最后一个数字 互质 ，则认为 nums[i] 和 nums[j] 是一组 美丽下标对 。

返回 nums 中 美丽下标对 的总数目。

对于两个整数 x 和 y ，如果不存在大于 1 的整数可以整除它们，则认为 x 和 y 互质 。换而言之，如果 gcd(x, y) == 1 ，则认为 x 和 y 互质，其中 gcd(x, y) 是 x 和 y 的 最大公因数 。


```
impl Solution {
    /// 计算美丽下标对的总数
    ///
    /// 美丽下标对：nums[i] 的首位与 nums[j] 的末位互质 (i < j)
    ///
    /// 时间复杂度：O(n * 10) ≈ O(n)，空间复杂度：O(1)
    pub fn count_beautiful_pairs(nums: Vec<i32>) -> i32 {
        let mut count = [0; 10];
        let mut total = 0;

        for &num in &nums {
            let last = (num % 10) as usize;

            // 当前数字作为 j，累加与末位互质的首位计数
            for first in 1..10 {
                if gcd(last, first) == 1 {
                    total += count[first];
                }
            }

            // 将当前数字的首位加入计数
            let mut first = num;
            while first >= 10 {
                first /= 10;
            }
            count[first as usize] += 1;
        }

        total
    }
}

/// 最大公约数（欧几里得算法）
#[inline]
fn gcd(mut a: usize, mut b: usize) -> usize {
    while b != 0 {
        let temp = b;
        b = a % b;
        a = temp;
    }
    a
}
```
