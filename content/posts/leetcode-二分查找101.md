---
title: "leetcode-二分查找101"
date: 2026-09-28T12:29:17+08:00
tags: ["leetcode", "二分查找"]
draft: false
---


## 稀疏数组搜索

稀疏数组搜索。有个排好序的字符串数组，其中散布着一些空字符串，编写一种方法，找出给定字符串的位置。


```
impl Solution {
    pub fn find_string(words: Vec<String>, s: String) -> i32 {
        let (mut left, mut right) = (0, words.len());
        while left < right {
            let mut mid = left + (right - left) / 2;
            // 遇到空字符串时向右找第一个非空位置
            while mid < right && words[mid].is_empty() {
                mid += 1;
            }
            if mid == right {
                // 右侧全为空，收缩右边界到原来 mid 的左侧继续查找
                right = left + (right - left) / 2;
                continue;
            }
            match s.as_str().cmp(words[mid].as_str()) {
                std::cmp::Ordering::Equal => return mid as i32,
                std::cmp::Ordering::Greater => left = mid + 1,
                std::cmp::Ordering::Less => right = mid,
            }
        }
        -1
    }
}
```
