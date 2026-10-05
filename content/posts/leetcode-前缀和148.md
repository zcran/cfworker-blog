---
title: "leetcode-前缀和148"
date: 2026-08-25T10:02:55+08:00
tags: ["leetcode", "前缀和"]
draft: false
---


## 字母与数字

给定一个放有字母和数字的数组，找到最长的子数组，且包含的字母和数字的个数相同。

返回该子数组，若存在多个最长子数组，返回左端点下标值最小的子数组。若不存在这样的数组，返回一个空数组。


```
impl Solution {
    pub fn find_longest_subarray(array: Vec<String>) -> Vec<String> {
        use std::collections::HashMap;

        let n = array.len();
        // map 记录每个前缀和第一次出现的位置（0-based）
        let mut first_pos = HashMap::with_capacity(n + 1);
        first_pos.insert(0, 0);

        let mut prefix_sum = 0;
        let mut start = 0;
        let mut max_len = 0;

        for (i, item) in array.iter().enumerate() {
            let idx = i + 1;
            // 数字视为 +1，字母视为 -1
            let is_digit = item.as_bytes()[0].is_ascii_digit();
            prefix_sum += if is_digit { 1 } else { -1 };

            // 如果当前前缀和已存在，则找到一个平衡子数组
            if let Some(&first) = first_pos.get(&prefix_sum) {
                let cur_len = idx - first;
                if cur_len > max_len {
                    max_len = cur_len;
                    start = first;
                }
            } else {
                // 只记录第一次出现的位置
                first_pos.insert(prefix_sum, idx);
            }
        }

        // 提取子数组
        if max_len == 0 {
            Vec::new()
        } else {
            array.into_iter().skip(start).take(max_len).collect()
        }
    }
}
```
