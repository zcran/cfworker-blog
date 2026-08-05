---
title: "leetcode-枚举72"
date: 2026-07-09T10:05:02+08:00
tags: ["leetcode", "枚举"]
draft: false
---


## 使字符频率相等的最少操作次数

给你一个字符串 s 。

如果字符串 t 中的字符出现次数相等，那么我们称 t 为 好的 。

你可以执行以下操作 任意次 ：

从 s 中删除一个字符。
往 s 中添加一个字符。
将 s 中一个字母变成字母表中下一个字母。

注意 ，第三个操作不能将 'z' 变为 'a' 。

请你返回将 s 变 好 的 最少 操作次数。


```
impl Solution {
    pub fn make_string_good(s: String) -> i32 {
        // 统计每个字母的出现次数
        let mut cnt = [0; 26];
        for ch in s.chars() {
            cnt[(ch as u8 - b'a') as usize] += 1;
        }

        let max_cnt = *cnt.iter().max().unwrap();
        let n = s.len() as i32;
        let mut ans = n; // target = 0 时的答案（删除所有字符）

        // 枚举目标值 target（每个字母最终出现的次数）
        for target in 1..=max_cnt {
            let mut dp = [0; 27];

            // 处理最后一个字母（索引 25）
            let diff = if cnt[25] >= target { cnt[25] - target } else { target - cnt[25] };
            dp[25] = cnt[25].min(diff);

            // 从倒数第二个字母往前处理（索引 24 到 0）
            for i in (0..=24).rev() {
                let x = cnt[i];
                let y = cnt[i + 1];

                // 方案1：单独处理 x（变成 target 或 0）
                let diff = if x >= target { x - target } else { target - x };
                let cost1 = dp[i + 1] + x.min(diff);
                let mut cost2 = i32::MAX;

                // 方案2：利用第三种操作，将 x 的多余部分补给 y
                // 只有当 y < target 时，才需要从 x 借字符
                if y < target {
                    // x 需要保留 target（如果 x > target）或全部删除（如果 x <= target）
                    let keep = if x > target { target } else { 0 };
                    let extra = x - keep; // x 可以提供的字符数
                    let need = target - y; // y 需要的字符数
                    cost2 = dp[i + 2] + extra.max(need);
                }

                dp[i] = cost1.min(cost2);
            }

            ans = ans.min(dp[0]);
        }

        ans
    }
}
```
