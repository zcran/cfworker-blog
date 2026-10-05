---
title: "leetcode-二分查找82"
date: 2026-09-28T12:29:15+08:00
tags: ["leetcode", "二分查找"]
draft: false
---


## 变为活跃状态的最小时间

给你一个长度为 n 的字符串 s 和一个整数数组 order，其中 order 是范围 [0, n - 1] 内数字的一个 排列。

从时间 t = 0 开始，在每个时间点，将字符串 s 中下标为 order[t] 的字符替换为 '*'。

如果 子字符串 包含 至少 一个 '*' ，则认为该子字符串有效。

如果字符串中 有效子字符串 的总数大于或等于 k，则称该字符串为 活跃 字符串。

返回字符串 s 变为 活跃 状态的最小时间 t。如果无法变为活跃状态，返回 -1。


```
impl Solution {
    pub fn min_time(s: String, order: Vec<i32>, k: i32) -> i32 {
        let n = s.len();
        let k = k as i64;

        // 总子串数不足 k，无法变为活跃
        if (n as i64) * (n as i64 + 1) / 2 < k {
            return -1;
        }

        // mark[i] == t 表示在第 t 步时下标 i 已变为 '*'
        let mut mark = vec![0_i32; n];

        // 判断执行前 m+1 步（即时刻 m）后是否活跃
        let is_active = |m: i32, mark: &mut Vec<i32>| -> bool {
            let t = m + 1;
            for &idx in order.iter().take(t as usize) {
                mark[idx as usize] = t;
            }
            let mut cnt = 0_i64;
            let mut last = -1_i64; // 上一个 '*' 的位置
            for i in 0..n {
                if mark[i] == t {
                    last = i as i64;
                }
                cnt += last + 1; // 以 i 结尾的有效子串数
                if cnt >= k {
                    return true;
                }
            }
            false
        };

        // 二分最小时间
        let mut left = -1_i32;
        let mut right = n as i32 - 1;
        while left + 1 < right {
            let mid = left + (right - left) / 2;
            if is_active(mid, &mut mark) {
                right = mid;
            } else {
                left = mid;
            }
        }
        right
    }
}
```
