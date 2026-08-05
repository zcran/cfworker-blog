---
title: "leetcode-滑动窗口82"
date: 2026-07-18T11:02:34+08:00
tags: ["leetcode", "滑动窗口"]
draft: false
---


## 找出出现至少三次的最长特殊子字符串 II

给你一个仅由小写英文字母组成的字符串 s 。

如果一个字符串仅由单一字符组成，那么它被称为 特殊 字符串。例如，字符串 "abc" 不是特殊字符串，而字符串 "ddd"、"zz" 和 "f" 是特殊字符串。

返回在 s 中出现 至少三次 的 最长特殊子字符串 的长度，如果不存在出现至少三次的特殊子字符串，则返回 -1 。

子字符串 是字符串中的一个连续 非空 字符序列。


```
use std::collections::HashMap;

impl Solution {
    pub fn maximum_length(s: String) -> i32 {
        let s = s.as_bytes();
        let n = s.len();

        // 收集每个字符的所有连续段长度
        let mut groups: HashMap<u8, Vec<i32>> = HashMap::new();
        let mut i = 0;
        while i < n {
            let mut j = i;
            while j < n && s[j] == s[i] {
                j += 1;
            }
            groups.entry(s[i]).or_insert_with(Vec::new).push((j - i) as i32);
            i = j;
        }

        let mut ans = -1;

        // 对每个字符，二分查找最大长度
        for lengths in groups.values() {
            // 只保留最大的3个长度即可（最优解一定来自最大的3个连续段）
            let mut top3 = lengths.clone();
            top3.sort_unstable_by(|a, b| b.cmp(a));
            top3.truncate(3);

            // 二分查找可达到的长度
            let mut lo = 1;
            let mut hi = *top3.first().unwrap_or(&0);

            while lo <= hi {
                let mid = (lo + hi) / 2;
                let mut count = 0;
                for &len in &top3 {
                    if len >= mid {
                        count += len - mid + 1;
                    }
                }

                if count >= 3 {
                    ans = ans.max(mid);
                    lo = mid + 1;
                } else {
                    hi = mid - 1;
                }
            }
        }

        ans
    }
}
```
