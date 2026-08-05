---
title: "leetcode-计数41"
date: 2026-08-01T10:40:53+08:00
tags: ["leetcode", "计数"]
draft: false
---


## 使所有区间的异或结果为零

给你一个整数数组 nums 和一个整数 k 。区间 [left, right]（left <= right）的 异或结果 是对下标位于 left 和 right（包括 left 和 right ）之间所有元素进行 XOR 运算的结果：nums[left] XOR nums[left+1] XOR ... XOR nums[right] 。

返回数组中 要更改的最小元素数 ，以使所有长度为 k 的区间异或结果等于零。


```
impl Solution {
    pub fn min_changes(nums: Vec<i32>, k: i32) -> i32 {
        let k = k as usize;
        let n = nums.len();
        const MAX_XOR: usize = 1024; // 因为 nums[i] < 1024

        // 按模 k 分组，统计每组中每个数字出现的次数
        let mut group_size = vec![0; k];
        let mut group_count = vec![std::collections::HashMap::new(); k];
        for (i, &num) in nums.iter().enumerate() {
            let g = i % k;
            group_size[g] += 1;
            *group_count[g].entry(num).or_insert(0) += 1;
        }

        // dp[i][x]：处理前 i+1 组，异或结果为 x 的最小修改次数
        let mut dp = vec![vec![i32::MAX; MAX_XOR]; k];

        // 初始化第 0 组
        let size0 = group_size[0];
        for x in 0..MAX_XOR {
            let keep = group_count[0].get(&(x as i32)).unwrap_or(&0);
            dp[0][x] = size0 - keep;
        }

        // 处理后续组
        for i in 1..k {
            let size = group_size[i];
            // 优化：从上一组取最小值，然后全部改成这个值（不保留任何原数字）
            let min_prev = dp[i - 1].iter().min().unwrap();
            let base = min_prev + size;
            dp[i].fill(base);

            // 保留当前组中的某些数字
            for (&num, &count) in &group_count[i] {
                let num = num as usize;
                for xor_val in 0..MAX_XOR {
                    if dp[i - 1][xor_val] < i32::MAX {
                        let new_xor = xor_val ^ num;
                        let cost = dp[i - 1][xor_val] + size - count;
                        if cost < dp[i][new_xor] {
                            dp[i][new_xor] = cost;
                        }
                    }
                }
            }
        }

        dp[k - 1][0]
    }
}
```
