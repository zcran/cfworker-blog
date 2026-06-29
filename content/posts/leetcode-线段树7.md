---
title: "leetcode-线段树7"
date: 2026-06-19T09:37:24+08:00
tags: ["leetcode", "线段树"]
draft: false
---


## 子数组按位与值为 K 的数目

给你一个整数数组 nums 和一个整数 k ，请你返回 nums 中有多少个子数组满足：子数组中所有元素按位 AND 的结果为 k 。


```
impl Solution {
    /// 统计子数组按位 AND 结果为 k 的数量
    ///
    /// 思路：对于每个右端点 i，维护所有以 i 结尾的子数组的 AND 值。
    /// 由于 AND 运算只会减少 1 的位，因此以固定右端点结尾的不同 AND 值最多 31 个。
    /// 使用 (and_value, count) 对来压缩存储，每次用当前元素更新所有可能的 AND 值。
    ///
    /// 时间复杂度 O(n * 31)，空间复杂度 O(31)
    pub fn count_subarrays(nums: Vec<i32>, k: i32) -> i64 {
        // 存储 (AND值, 出现次数) 对，表示以当前右端点结尾的所有子数组
        let mut and_counts = Vec::new();
        let mut ans = 0i64;

        for &num in &nums {
            // 当前元素单独作为子数组
            let mut new_counts = vec![(num, 1)];

            // 将当前元素与之前所有 AND 值组合
            for &(prev_and, count) in &and_counts {
                let new_and = prev_and & num;
                // 合并相同的 AND 值
                if let Some(last) = new_counts.last_mut() {
                    if last.0 == new_and {
                        last.1 += count;
                        continue;
                    }
                }
                new_counts.push((new_and, count));
            }

            and_counts = new_counts;

            // 统计当前所有子数组中 AND 值等于 k 的数量
            for &(and_val, count) in &and_counts {
                if and_val == k {
                    ans += count as i64;
                }
            }
        }

        ans
    }
}
```
