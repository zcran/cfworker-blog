---
title: "leetcode-枚举78"
date: 2026-07-09T10:05:03+08:00
tags: ["leetcode", "枚举"]
draft: false
---


## 奇偶频次间的最大差值 II

给你一个字符串 s 和一个整数 k 。请你找出 s 的子字符串 subs 中两个字符的出现频次之间的 最大 差值，freq[a] - freq[b] ，其中：

subs 的长度 至少 为 k 。
字符 a 在 subs 中出现奇数次。
字符 b 在 subs 中出现非 0 偶数次。

返回 最大 差值。

注意 ，subs 可以包含超过 2 个 互不相同 的字符。

子字符串 是字符串中的一个连续字符序列。


```
impl Solution {
    pub fn max_difference(s: String, k: i32) -> i32 {
        let s = s.as_bytes();
        let n = s.len() as i32;
        let k = k as usize;
        let mut ans = i32::MIN;

        // 状态编码：高位表示 cnt_a 奇偶性，低位表示 cnt_b 奇偶性
        // 状态 0: (偶,偶), 1: (偶,奇), 2: (奇,偶), 3: (奇,奇)
        fn encode(cnt_a: i32, cnt_b: i32) -> usize {
            (((cnt_a & 1) << 1) | (cnt_b & 1)) as usize
        }

        // 枚举字符 a（出现奇数次）和 b（出现偶数次且非零）
        for a in b'0'..=b'4' {
            for b in b'0'..=b'4' {
                if a == b {
                    continue;
                }

                // best[状态] = 满足条件的子串中 (prev_a - prev_b) 的最小值
                let mut best = [i32::MAX; 4];

                let mut cnt_a = 0;
                let mut cnt_b = 0;
                let mut prev_a = 0;
                let mut prev_b = 0;
                let mut left = -1_i32;

                for right in 0..n as i32 {
                    // 更新当前字符计数
                    if s[right as usize] == a {
                        cnt_a += 1;
                    }
                    if s[right as usize] == b {
                        cnt_b += 1;
                    }

                    // 移动左指针：确保窗口长度 >= k，且 b 的当前计数 - 之前计数 >= 2
                    // 即保证 subs 中 b 至少出现 2 次
                    while right - left >= k as i32 && cnt_b - prev_b >= 2 {
                        let left_status = encode(prev_a, prev_b);
                        best[left_status] = best[left_status].min(prev_a - prev_b);

                        left += 1;
                        if s[left as usize] == a {
                            prev_a += 1;
                        }
                        if s[left as usize] == b {
                            prev_b += 1;
                        }
                    }

                    // 当前状态需要满足：cnt_a 为奇数，cnt_b 为偶数
                    let right_status = encode(cnt_a, cnt_b);
                    // 我们需要右侧状态为 (奇, 偶)，即状态 2
                    // 如果左侧状态是 (偶, 偶) 或 (偶, 奇)，则右侧状态为 (奇, 偶)
                    // 异或 0b10 将高位取反，低位不变：状态 ^ 2 表示 (cnt_a 奇偶性反转)
                    let need_status = right_status ^ 0b10;
                    if best[need_status] != i32::MAX {
                        ans = ans.max(cnt_a - cnt_b - best[need_status]);
                    }
                }
            }
        }

        ans
    }
}
```
