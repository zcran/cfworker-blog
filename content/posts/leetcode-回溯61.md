---
title: "leetcode-回溯61"
date: 2026-07-04T10:22:04+08:00
tags: ["leetcode", "回溯"]
draft: false
---


## 根据模式串构造最小数字

给你下标从 0 开始、长度为 n 的字符串 pattern ，它包含两种字符，'I' 表示 上升 ，'D' 表示 下降 。

你需要构造一个下标从 0 开始长度为 n + 1 的字符串，且它要满足以下条件：

num 包含数字 '1' 到 '9' ，其中每个数字 至多 使用一次。
如果 pattern[i] == 'I' ，那么 num[i] < num[i + 1] 。
如果 pattern[i] == 'D' ，那么 num[i] > num[i + 1] 。

请你返回满足上述条件字典序 最小 的字符串 num。


```
impl Solution {
    pub fn smallest_number(mut pattern: String) -> String {
        // 添加哨兵，方便处理最后的连续下降段
        pattern.push('I');
        let pattern = pattern.as_bytes();
        let mut ans = String::with_capacity(pattern.len());
        let mut start = 0;

        // 遍历每个位置，遇到 'I' 时处理从 start 到当前位置的下降段
        for i in 0..pattern.len() {
            if pattern[i] == b'I' {
                // 从当前最大数字倒序填入，保证下降段数字递减
                for num in (start + 1..=i + 1).rev() {
                    ans.push((num as u8 + b'0') as char);
                }
                start = i + 1; // 更新起始位置
            }
        }

        ans
    }
}
```
