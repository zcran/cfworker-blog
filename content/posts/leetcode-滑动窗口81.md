---
title: "leetcode-滑动窗口81"
date: 2026-07-18T11:02:33+08:00
tags: ["leetcode", "滑动窗口"]
draft: false
---


## 找出出现至少三次的最长特殊子字符串 I

给你一个仅由小写英文字母组成的字符串 s 。

如果一个字符串仅由单一字符组成，那么它被称为 特殊 字符串。例如，字符串 "abc" 不是特殊字符串，而字符串 "ddd"、"zz" 和 "f" 是特殊字符串。

返回在 s 中出现 至少三次 的 最长特殊子字符串 的长度，如果不存在出现至少三次的特殊子字符串，则返回 -1 。

子字符串 是字符串中的一个连续 非空 字符序列。


```
impl Solution {
    pub fn maximum_length(s: String) -> i32 {
        let s = s.as_bytes();
        let n = s.len();
        // 存储每个字符对应的所有连续段长度（只保留最大的3个）
        let mut groups: [Vec<i32>; 26] = std::array::from_fn(|_| Vec::with_capacity(3));

        let mut i = 0;
        while i < n {
            let ch = (s[i] - b'a') as usize;
            let mut j = i;
            while j < n && s[j] == s[i] {
                j += 1;
            }
            let len = (j - i) as i32;

            // 只保留最大的3个长度
            let g = &mut groups[ch];
            g.push(len);
            g.sort_unstable_by(|a, b| b.cmp(a)); // 降序
            if g.len() > 3 {
                g.pop();
            }

            i = j;
        }

        let mut ans = -1;
        for g in groups.iter() {
            if g.is_empty() {
                continue;
            }

            // 情况1: 从最长段中取 len-2（分成3段，每段长度至少为 len-2）
            if g[0] > 2 {
                ans = ans.max(g[0] - 2);
            }

            // 情况2: 从最长段取 len-1，与第二长段取 min
            if g.len() > 1 && g[0] > 1 {
                ans = ans.max((g[0] - 1).min(g[1]));
            }

            // 情况3: 从第三长段取全部
            if g.len() > 2 {
                ans = ans.max(g[2]);
            }
        }

        ans
    }
}
```
