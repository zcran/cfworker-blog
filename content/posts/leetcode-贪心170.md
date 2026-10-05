---
title: "leetcode-贪心170"
date: 2026-10-03T12:55:45+08:00
tags: ["leetcode", "贪心"]
draft: false
---


## 使数组奇偶交替的最少操作

给你一个整数数组 nums。

如果对于每一个下标 i（其中 0 <= i < n - 1），nums[i] 和 nums[i + 1] 具有不同的奇偶性（一个是偶数，另一个是奇数），则该数组被称为 奇偶交替 的。

在一次操作中，你可以选择任意下标 i，并将 nums[i] 增加 1 或减少 1。

返回一个长度为 2 的整数数组 answer，其中：

· answer[0] 是使数组变为奇偶交替所需的 最少 操作次数。
· answer[1] 是在所有通过执行 恰好 answer[0] 次操作获得的奇偶交替数组中，max(nums) - min(nums) 的 最小 可能值。

长度为 1 的数组被认为是奇偶交替的。


```
impl Solution {
    pub fn make_parity_alternating(nums: Vec<i32>) -> Vec<i32> {
        if nums.len() == 1 {
            return vec![0, 0];
        }

        // 全局最小值和最大值，用于处理"调整时避免扩大极差"的情况
        let g_min = *nums.iter().min().unwrap();
        let g_max = *nums.iter().max().unwrap();

        // target = 0 或 1，表示目标奇偶模式：
        // 位置 i 的奇偶性需等于 target ^ (i % 2)
        let calc = |target: i32| -> Vec<i32> {
            let mut ops = 0i32;
            let mut mn = i32::MAX;
            let mut mx = i32::MIN;

            for (i, &x) in nums.iter().enumerate() {
                let mut val = x;
                // 判断当前奇偶性是否符合目标模式
                if ((x - i as i32) & 1) != target {
                    ops += 1;
                    // 调整时优先朝"不扩大极差"的方向：最小值 +1，最大值 -1
                    if x == g_min {
                        val = x + 1;
                    } else if x == g_max {
                        val = x - 1;
                    }
                    // 其余情况奇偶翻转本身不改变数值大小，极差由原值决定
                }
                mn = mn.min(val);
                mx = mx.max(val);
            }
            // n >= 2 时，奇偶交替的数组极差至少为 1
            vec![ops, (mx - mn).max(1)]
        };

        let r0 = calc(0);
        let r1 = calc(1);

        // 取操作次数更少者；若相同，取极差更小者
        if r0[0] < r1[0] || (r0[0] == r1[0] && r0[1] <= r1[1]) {
            r0
        } else {
            r1
        }
    }
}
```
