---
title: "leetcode-组合数学24"
date: 2026-05-24T09:54:13+08:00
tags: ["leetcode", "组合数学"]
draft: false
---


## 统计好分割方案的数目


给你一个下标从 0 开始、由 正整数 组成的数组 nums。

将数组分割成一个或多个 连续 子数组，如果不存在包含了相同数字的两个子数组，则认为是一种 好分割方案 。

返回 nums 的 好分割方案 的 数目。

由于答案可能很大，请返回答案对 10^9 + 7 取余 的结果。


```
impl Solution {
    pub fn number_of_good_partitions(nums: Vec<i32>) -> i32 {
        const MOD: i64 = 1_000_000_007;

        // 1. 记录每个数字最后出现的位置
        let mut last_pos = std::collections::HashMap::new();
        for (i, &num) in nums.iter().enumerate() {
            last_pos.insert(num, i);
        }

        // 2. 扫描数组，找出可以独立分割的段数
        let mut segments = 0;      // 独立段数量
        let mut cur_end = 0;       // 当前段必须覆盖到的最右边界
        for (i, &num) in nums.iter().enumerate() {
            // 更新当前段的右边界（必须包含该数字的最后出现位置）
            cur_end = cur_end.max(*last_pos.get(&num).unwrap());
            // 如果当前位置等于右边界，说明一个独立段结束
            if i == cur_end {
                segments += 1;
            }
        }

        // 3. 答案为 2^(segments-1) % MOD
        let mut ans = 1i64;
        let exp = segments - 1;     // 至少有一个段，exp >= 0
        for _ in 0..exp {
            ans = (ans * 2) % MOD;
        }
        ans as i32
    }
}
```
