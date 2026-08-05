---
title: "leetcode-计数49"
date: 2026-08-01T10:40:53+08:00
tags: ["leetcode", "计数"]
draft: false
---


## 连接后等于目标字符串的字符串对

给你一个 数字 字符串数组 nums 和一个 数字 字符串 target ，请你返回 nums[i] + nums[j] （两个字符串连接）结果等于 target 的下标 (i, j) （需满足 i != j）的数目。


```
impl Solution {
    pub fn num_of_pairs(nums: Vec<String>, target: String) -> i32 {
        use std::collections::HashMap;

        // 统计每个字符串出现的次数
        let mut count = HashMap::with_capacity(nums.len());
        for s in nums {
            *count.entry(s).or_insert(0) += 1;
        }

        let mut ans = 0;
        let target_bytes = target.as_bytes();
        let target_len = target_bytes.len();

        // 枚举所有可能的前缀长度
        for split in 1..target_len {
            let (prefix, suffix) = target_bytes.split_at(split);
            // 将字节切片转为字符串（跳过无效UTF-8检查，因为target是合法字符串）
            if let (Ok(prefix_str), Ok(suffix_str)) = (
                std::str::from_utf8(prefix),
                std::str::from_utf8(suffix),
            ) {
                let prefix_count = count.get(prefix_str).unwrap_or(&0);
                let suffix_count = count.get(suffix_str).unwrap_or(&0);

                if prefix_str == suffix_str {
                    // 前缀和后缀相同，需要 i != j
                    ans += prefix_count * (prefix_count - 1);
                } else {
                    ans += prefix_count * suffix_count;
                }
            }
        }

        ans as i32
    }
}
```
