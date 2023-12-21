---
title: "Leetcode 桶排序2"
date: 2023-12-19T15:46:22+08:00
tags: ["leetcode", "桶排序"]
draft: false
---

## 根据字符出现频率排序

给定一个字符串 s ，根据字符出现的 频率 对其进行 降序排序 。一个字符出现的 频率 是它出现在字符串中的次数。

返回 已排序的字符串 。如果有多个答案，返回其中任何一个。


```
impl Solution {
    pub fn frequency_sort(s: String) -> String {
        let mut bucket = vec![(0,0);256];
        for b in s.bytes() {
            bucket[b as usize].0 += 1;
            bucket[b as usize].1 = b;
        }
        bucket.sort_by(|a, b| b.0.cmp(&a.0));
        let mut res = String::new();
        for (times, ch) in bucket {
            for _ in 0..times {
                res.push(ch as char);
            }
        }
        res
    }
}
```
