---
title: "leetcode-并查集42"
date: 2026-06-25T11:22:23+08:00
tags: ["leetcode", "并查集"]
draft: false
---


## 字符串分组

给你一个下标从 0 开始的字符串数组 words 。每个字符串都只包含 小写英文字母 。words 中任意一个子串中，每个字母都至多只出现一次。

如果通过以下操作之一，我们可以从 s1 的字母集合得到 s2 的字母集合，那么我们称这两个字符串为 关联的 ：

· 往 s1 的字母集合中添加一个字母。
· 从 s1 的字母集合中删去一个字母。
· 将 s1 中的一个字母替换成另外任意一个字母（也可以替换为这个字母本身）。

数组 words 可以分为一个或者多个无交集的 组 。如果一个字符串与另一个字符串关联，那么它们应当属于同一个组。

注意，你需要确保分好组后，一个组内的任一字符串与其他组的字符串都不关联。可以证明在这个条件下，分组方案是唯一的。

请你返回一个长度为 2 的数组 ans ：

· ans[0] 是 words 分组后的 总组数 。
· ans[1] 是字符串数目最多的组所包含的字符串数目。



```
use std::collections::{HashMap, HashSet, VecDeque};

impl Solution {
    pub fn group_strings(words: Vec<String>) -> Vec<i32> {
        // 将字符串转换为位掩码（每个字母占一位）
        fn to_mask(s: &str) -> i32 {
            let mut mask = 0;
            for ch in s.chars() {
                mask |= 1 << (ch as u8 - b'a') as i32;
            }
            mask
        }

        // 统计每种掩码的出现次数
        let mut count = HashMap::new();
        for word in &words {
            *count.entry(to_mask(word)).or_insert(0) += 1;
        }

        // 生成所有与当前掩码关联的掩码（通过增、删、改一个字母）
        fn get_related(mask: i32) -> Vec<i32> {
            let mut res = Vec::new();
            for i in 0..26 {
                let bit = 1 << i;
                // 删除或添加字母 i
                res.push(mask ^ bit);

                // 如果字母 i 存在，替换为另一个字母 j
                if (mask & bit) != 0 {
                    for j in 0..26 {
                        if (mask & (1 << j)) == 0 {
                            res.push(mask ^ bit ^ (1 << j));
                        }
                    }
                }
            }
            res
        }

        let mut visited = HashSet::new();
        let mut groups = 0;
        let mut max_size = 0;

        for (&mask, &freq) in &count {
            if visited.contains(&mask) {
                continue;
            }

            // BFS 遍历连通分量
            let mut queue = VecDeque::new();
            queue.push_back(mask);
            visited.insert(mask);
            let mut size = freq;

            while let Some(cur) = queue.pop_front() {
                for next in get_related(cur) {
                    if let Some(&next_freq) = count.get(&next) {
                        if !visited.contains(&next) {
                            visited.insert(next);
                            size += next_freq;
                            queue.push_back(next);
                        }
                    }
                }
            }

            groups += 1;
            max_size = max_size.max(size);
        }

        vec![groups, max_size]
    }
}
```
