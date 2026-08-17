---
title: "leetcode-模拟68"
date: 2026-08-08T11:31:12+08:00
tags: ["leetcode", "模拟"]
draft: false
---


## 计算字符串的数字和

给你一个由若干数字（0 - 9）组成的字符串 s ，和一个整数。

如果 s 的长度大于 k ，则可以执行一轮操作。在一轮操作中，需要完成以下工作：

1. 将 s 拆分 成长度为 k 的若干 连续数字组 ，使得前 k 个字符都分在第一组，接下来的 k 个字符都分在第二组，依此类推。注意，最后一个数字组的长度可以小于 k 。
2. 用表示每个数字组中所有数字之和的字符串来 替换 对应的数字组。例如，"346" 会替换为 "13" ，因为 3 + 4 + 6 = 13 。
3. 合并 所有组以形成一个新字符串。如果新字符串的长度大于 k 则重复第一步。

返回在完成所有轮操作后的 s 。


```
impl Solution {
    /// 反复将字符串按长度 k 分组，每组替换为组内数字之和，
    /// 直到结果长度不超过 k。
    pub fn digit_sum(mut s: String, k: i32) -> String {
        let k = k as usize;
        while s.len() > k {
            s = s
                .as_bytes()
                .chunks(k)
                .map(|chunk| {
                    chunk
                        .iter()
                        .map(|&b| (b - b'0') as u32)
                        .sum::<u32>()
                        .to_string()
                })
                .collect();
        }
        s
    }
}
```
