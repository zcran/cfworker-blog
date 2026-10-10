---
title: "leetcode-排序20"
date: 2026-10-05T00:35:00+08:00
tags: ["leetcode", "排序"]
draft: false
---


## 重新排列日志文件

给你一个日志数组 logs。每条日志都是以空格分隔的字串，其第一个字为字母与数字混合的 标识符 。

有两种不同类型的日志：

字母日志：除标识符之外，所有字均由小写字母组成
数字日志：除标识符之外，所有字均由数字组成

请按下述规则将日志重新排序：

所有 字母日志 都排在 数字日志 之前。
字母日志 在内容不同时，忽略标识符后，按内容 字典顺序 排序；在内容相同时，按标识符排序。
数字日志 应该保留原来的相对顺序。

返回日志的 最终 顺序。


```
use std::cmp::Ordering;

impl Solution {
    /// 按规则重排日志
    ///
    /// 规则：
    ///   - 字母日志全部排在数字日志之前；
    ///   - 字母日志先按内容字典序，再按标识符字典序；
    ///   - 数字日志保持原有相对顺序。
    /// 由于 sort_by 是稳定排序，数字日志之间返回 Equal 即可保序。
    /// 时间 O(n log n · m)，额外空间 O(1)（不含排序递归栈）。
    pub fn reorder_log_files(mut logs: Vec<String>) -> Vec<String> {
        // 一次 splitn 拆出标识符与正文，避免重复拆分
        logs.sort_by(|a, b| {
            let (id_a, body_a) = a.split_once(' ').unwrap();
            let (id_b, body_b) = b.split_once(' ').unwrap();

            let a_digit = body_a.as_bytes()[0].is_ascii_digit();
            let b_digit = body_b.as_bytes()[0].is_ascii_digit();

            match (a_digit, b_digit) {
                // 数字日志之间视为相等，稳定排序保持原相对顺序
                (true, true) => Ordering::Equal,
                // 字母日志：先比正文，再比标识符
                (false, false) => (body_a, id_a).cmp(&(body_b, id_b)),
                // 字母日志排在数字日志之前
                (false, true) => Ordering::Less,
                (true, false) => Ordering::Greater,
            }
        });

        logs
    }
}
```
