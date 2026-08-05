---
title: "leetcode-枚举75"
date: 2026-07-09T10:05:03+08:00
tags: ["leetcode", "枚举"]
draft: false
---


## 统计特殊子序列的数目

给你一个只包含正整数的数组 nums 。

特殊子序列 是一个长度为 4 的子序列，用下标 (p, q, r, s) 表示，它们满足 p < q < r < s ，且这个子序列 必须 满足以下条件：

nums[p] * nums[r] == nums[q] * nums[s]

相邻坐标之间至少间隔 一个 数字。换句话说，q - p > 1 ，r - q > 1 且 s - r > 1 。

子序列指的是从原数组中删除零个或者更多元素后，剩下元素不改变顺序组成的数字序列。

请你返回 nums 中不同 特殊子序列 的数目。


```
use std::collections::HashMap;

impl Solution {
    pub fn number_of_subsequences(nums: Vec<i32>) -> i64 {
        // 辅助函数：计算最大公约数
        fn gcd(mut a: i32, mut b: i32) -> i32 {
            while b != 0 {
                let tmp = b;
                b = a % b;
                a = tmp;
            }
            a
        }

        // 将分数 a/b 化简为最简形式
        fn reduce_fraction(a: i32, b: i32) -> (i32, i32) {
            let g = gcd(a, b);
            (a / g, b / g)
        }

        let n = nums.len();
        let mut ans = 0i64;
        // freq 存储已经出现的 (nums[p] / nums[q]) 的最简分数及其计数
        let mut freq: HashMap<(i32, i32), i64> = HashMap::new();

        // 枚举中间的两个位置 q 和 r（对应原代码中的 v2 和 v3）
        // v2 和 v3 分别对应 q 和 r，满足 q < r 且 r - q > 1
        // 因为需要 p < q - 1 且 s > r + 1
        for q in 2..n.saturating_sub(4) {
            let r = q + 2; // 确保 r - q > 1

            // 枚举左侧 p：p < q - 1
            // 将 nums[p] / nums[q] 加入哈希表
            for p in 0..q.saturating_sub(1) {
                let key = reduce_fraction(nums[p], nums[q]);
                *freq.entry(key).or_insert(0) += 1;
            }

            // 枚举右侧 s：s > r + 1
            // 查找 nums[s] / nums[r] 是否存在于哈希表中
            for s in (r + 2)..n {
                let key = reduce_fraction(nums[s], nums[r]);
                if let Some(&count) = freq.get(&key) {
                    ans += count;
                }
            }
        }

        ans
    }
}
```
