---
title: "leetcode-枚举101"
date: 2026-07-09T10:05:04+08:00
tags: ["leetcode", "枚举"]
draft: false
---


## 使数组变为模交替数组的最少操作次数 I

给你一个整数数组 nums 和一个整数 k 。

在一步操作中，你可以将 nums 中的任意元素 增加 或 减少 1 。

如果存在两个 不同 的整数 x 和 y （0 <= x, y < k）满足以下条件，则称数组为 模交替 数组：

对于每个 偶数 下标 i ，nums[i] % k == x
对于每个 奇数 下标 i ，nums[i] % k == y

返回使 nums 成为 模交替 数组所需的 最少 操作次数。


```
impl Solution {
    pub fn min_operations(nums: Vec<i32>, k: i32) -> i32 {
        let k = k as i32;
        let mut ans = i32::MAX;

        // 枚举所有可能的 (x, y) 组合，x 和 y 不同
        for x in 0..k {
            for y in 0..k {
                if x == y {
                    continue;
                }

                let mut cost = 0;
                for (i, &num) in nums.iter().enumerate() {
                    // 计算当前数模 k 后的值
                    let remainder = num % k;
                    // 计算到目标值的最小距离（考虑循环）
                    let target = if i % 2 == 0 { x } else { y };
                    let diff = (remainder - target).abs();
                    cost += diff.min(k - diff);

                    // 如果已经超过当前最优，提前终止
                    if cost >= ans {
                        break;
                    }
                }
                ans = ans.min(cost);
            }
        }

        ans
    }
}
```
