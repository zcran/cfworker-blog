---
title: "leetcode-有序集合29"
date: 2026-06-14T10:47:42+08:00
tags: ["leetcode", "有序集合"]
draft: false
---


## 最高频率的 ID

你需要在一个集合里动态记录 ID 的出现频率。给你两个长度都为 n 的整数数组 nums 和 freq ，nums 中每一个元素表示一个 ID ，对应的 freq 中的元素表示这个 ID 在集合中此次操作后需要增加或者减少的数目。

· 增加 ID 的数目：如果 freq[i] 是正数，那么 freq[i] 个 ID 为 nums[i] 的元素在第 i 步操作后会添加到集合中。
· 减少 ID 的数目：如果 freq[i] 是负数，那么 -freq[i] 个 ID 为 nums[i] 的元素在第 i 步操作后会从集合中删除。

请你返回一个长度为 n 的数组 ans ，其中 ans[i] 表示第 i 步操作后出现频率最高的 ID 数目 ，如果在某次操作后集合为空，那么 ans[i] 为 0 。


```
use std::collections::{HashMap, BTreeMap};

impl Solution {
    pub fn most_frequent_i_ds(nums: Vec<i32>, freq: Vec<i32>) -> Vec<i64> {
        let n = nums.len();
        let mut cnt = HashMap::new();        // id -> 当前频率
        let mut freq_map = BTreeMap::new();  // 频率 -> 该频率的id数量

        let mut ans = Vec::with_capacity(n);

        for i in 0..n {
            let id = nums[i] as usize;
            let delta = freq[i] as i64;

            // 获取当前频率并移除旧的频率计数
            let old = cnt.get(&id).copied().unwrap_or(0);
            if old > 0 {
                let count = freq_map.entry(old).or_insert(0);
                *count -= 1;
                if *count == 0 {
                    freq_map.remove(&old);
                }
            }

            // 更新频率
            let new = old + delta;
            cnt.insert(id, new);

            if new > 0 {
                *freq_map.entry(new).or_insert(0) += 1;
            }

            // 获取当前最大频率
            let max_freq = freq_map.last_key_value().map(|(&k, _)| k).unwrap_or(0);
            ans.push(max_freq);
        }

        ans
    }
}
```
