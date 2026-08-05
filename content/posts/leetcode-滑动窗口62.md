---
title: "leetcode-滑动窗口62"
date: 2026-07-18T11:02:32+08:00
tags: ["leetcode", "滑动窗口"]
draft: false
---


## 每种字符至少取 K 个

给你一个由字符 'a'、'b'、'c' 组成的字符串 s 和一个非负整数 k 。每分钟，你可以选择取走 s 最左侧 还是 最右侧 的那个字符。

你必须取走每种字符 至少 k 个，返回需要的 最少 分钟数；如果无法取到，则返回 -1 。


```
impl Solution {
    pub fn take_characters(s: String, k: i32) -> i32 {
        let n = s.len();
        let k = k as usize;
        let chars: Vec<char> = s.chars().collect();

        // 统计字符串中每种字符的总数
        let mut total = [0; 3];
        for &ch in &chars {
            total[(ch as u8 - b'a') as usize] += 1;
        }

        // 如果某种字符总数少于k，无法满足条件
        if total[0] < k || total[1] < k || total[2] < k {
            return -1;
        }

        // 问题转化为：找最长子串，使得该子串外的每种字符数量至少为k
        // 等价于：找最长子串，其中每种字符出现次数不超过 total[i] - k
        let max_allowed = [total[0] - k, total[1] - k, total[2] - k];

        let mut window_count = [0; 3];
        let mut left = 0;
        let mut max_middle_len = 0;

        for right in 0..n {
            let idx = (chars[right] as u8 - b'a') as usize;
            window_count[idx] += 1;

            // 如果窗口中某种字符数量超过允许值，移动左指针
            while left <= right &&
                  (window_count[0] > max_allowed[0] ||
                   window_count[1] > max_allowed[1] ||
                   window_count[2] > max_allowed[2]) {
                let left_idx = (chars[left] as u8 - b'a') as usize;
                window_count[left_idx] -= 1;
                left += 1;
            }

            // 更新最大中间段长度
            max_middle_len = max_middle_len.max(right - left + 1);
        }

        // 最少分钟数 = 总长度 - 最长可保留的中间段长度
        (n - max_middle_len) as i32
    }
}
```
