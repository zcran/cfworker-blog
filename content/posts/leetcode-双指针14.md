---
title: "leetcode-双指针14"
date: 2026-09-05T09:53:01+08:00
tags: ["leetcode", "双指针"]
draft: false
---


## 比较版本号

给你两个 版本号字符串 version1 和 version2 ，请你比较它们。版本号由被点 '.' 分开的修订号组成。修订号的值 是它 转换为整数 并忽略前导零。

比较版本号时，请按 从左到右的顺序 依次比较它们的修订号。如果其中一个版本字符串的修订号较少，则将缺失的修订号视为 0。

返回规则如下：

如果 version1 < version2 返回 -1，
如果 version1 > version2 返回 1，
除此之外返回 0。


```
impl Solution {
    /// 比较两个版本号
    /// 时间复杂度 O(n+m)，空间复杂度 O(1)（忽略 split 产生的迭代器开销）
    /// 使用迭代器同时遍历两个版本号的修订号，缺失的视为 0
    pub fn compare_version(version1: String, version2: String) -> i32 {
        let mut v1 = version1.split('.').map(|s| s.parse::<i32>().unwrap_or(0));
        let mut v2 = version2.split('.').map(|s| s.parse::<i32>().unwrap_or(0));

        loop {
            match (v1.next(), v2.next()) {
                (Some(a), Some(b)) => {
                    if a != b {
                        return if a < b { -1 } else { 1 };
                    }
                }
                (Some(a), None) => {
                    if a != 0 {
                        return 1;
                    }
                }
                (None, Some(b)) => {
                    if b != 0 {
                        return -1;
                    }
                }
                (None, None) => return 0,
            }
        }
    }
}
```
