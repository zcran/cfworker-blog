---
title: "leetcode-计数91"
date: 2026-08-01T10:40:56+08:00
tags: ["leetcode", "计数"]
draft: false
---


## 进行操作使字符串为空

给你一个字符串 s 。

请你进行以下操作直到 s 为 空 ：

· 每次操作 依次 遍历 'a' 到 'z'，如果当前字符出现在 s 中，那么删除出现位置 最早 的该字符（如果存在的话）。

例如，最初 s = "aabcbbca"。我们执行下述操作：

· 移除下划线的字符  s = "aabcbbca"。结果字符串为 s = "abbca"。
· 移除下划线的字符  s = "abbca"。结果字符串为 s = "ba"。
· 移除下划线的字符  s = "ba"。结果字符串为 s = ""。

请你返回进行 最后 一次操作 之前 的字符串 s 。在上面的例子中，答案是 "ba"。


```
impl Solution {
    pub fn last_non_empty_string(s: String) -> String {
        let bytes = s.as_bytes();
        let mut cnt = [0; 26];
        let mut last = [0; 26];

        // 统计每个字符的出现次数和最后位置
        for (i, &b) in bytes.iter().enumerate() {
            let idx = (b - b'a') as usize;
            cnt[idx] += 1;
            last[idx] = i;
        }

        // 找到最大出现次数
        let mx = *cnt.iter().max().unwrap();

        // 收集出现次数等于 mx 的字符的最后位置，并按位置排序
        let mut ids: Vec<usize> = (0..26)
            .filter(|&i| cnt[i] == mx)
            .map(|i| last[i])
            .collect();
        ids.sort_unstable();

        // 按排序后的位置从原字符串取字符组成答案
        ids.iter()
            .map(|&i| bytes[i] as char)
            .collect()
    }
}
```
