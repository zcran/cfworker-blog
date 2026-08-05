---
title: "leetcode-滑动窗口3"
date: 2026-07-18T11:02:29+08:00
tags: ["leetcode", "滑动窗口"]
draft: false
---


## 最小覆盖子串

给定两个字符串 s 和 t，长度分别是 m 和 n，返回 s 中的 最短窗口 子串，使得该子串包含 t 中的每一个字符（包括重复字符）。如果没有这样的子串，返回空字符串 ""。

测试用例保证答案唯一。


```
impl Solution {
    /// 最小覆盖子串
    ///
    /// 滑动窗口 + 计数数组，O(n) 时间，O(1) 空间
    pub fn min_window(s: String, t: String) -> String {
        let s_bytes = s.as_bytes();
        let t_bytes = t.as_bytes();

        // 使用固定大小数组统计字符频率 (ASCII)
        let mut need = [0; 128];
        let mut have = [0; 128];

        // 统计 t 中每个字符的需求量
        for &c in t_bytes {
            need[c as usize] += 1;
        }

        // 计算需要匹配的不同字符数
        let required = need.iter().filter(|&&x| x > 0).count();
        let mut matched = 0; // 已满足需求的字符种类数

        let mut left = 0;
        let mut min_len = usize::MAX;
        let mut result_start = 0;

        for (right, &c) in s_bytes.iter().enumerate() {
            let idx = c as usize;
            have[idx] += 1;

            // 当前字符恰好满足需求时，匹配数+1
            if have[idx] == need[idx] {
                matched += 1;
            }

            // 尝试收缩窗口
            while matched == required {
                let cur_len = right - left + 1;
                if cur_len < min_len {
                    min_len = cur_len;
                    result_start = left;
                }

                // 移除左边界字符
                let left_idx = s_bytes[left] as usize;
                have[left_idx] -= 1;
                if have[left_idx] < need[left_idx] {
                    matched -= 1;
                }
                left += 1;
            }
        }

        if min_len == usize::MAX {
            String::new()
        } else {
            s[result_start..result_start + min_len].to_string()
        }
    }
}
```
