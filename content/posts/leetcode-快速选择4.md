---
title: "Leetcode 快速选择4"
date: 2023-12-19T15:49:31+08:00
tags: ["leetcode", "快速选择"]
draft: false
---

## 找出数组中的第 K 大整数

给你一个字符串数组 nums 和一个整数 k 。nums 中的每个字符串都表示一个不含前导零的整数。

返回 nums 中表示第 k 大整数的字符串。

注意：重复的数字在统计时会视为不同元素考虑。例如，如果 nums 是 ["1","2","2"]，那么 "2" 是最大的整数，"2" 是第二大的整数，"1" 是第三大的整数。

```
impl Solution {
    pub fn kth_largest_number(mut nums: Vec<String>, k: i32) -> String {
        // 自定义比较函数，在 a 对应的整数较大时返回 true，反之返回 false
        nums.sort_by(|a, b| {
			// 首先比较字符串长度
            if a.len() > b.len() {
                return std::cmp::Ordering::Greater;
            } else if a.len() < b.len() {
                return std::cmp::Ordering::Less;
            } else {
                // 长度相等时比较字符串字典序大小
                a.cmp(&b)
            }
        });

        nums[nums.len() - k as usize].to_string()
    }
}
```