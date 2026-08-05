---
title: "leetcode-枚举30"
date: 2026-07-09T10:05:00+08:00
tags: ["leetcode", "枚举"]
draft: false
---


## 公司命名

给你一个字符串数组 ideas 表示在公司命名过程中使用的名字列表。公司命名流程如下：

1.从 ideas 中选择 2 个 不同 名字，称为 ideaA 和 ideaB 。
2.交换 ideaA 和 ideaB 的首字母。
3.如果得到的两个新名字 都 不在 ideas 中，那么 ideaA ideaB（串联 ideaA 和 ideaB ，中间用一个空格分隔）是一个有效的公司名字。
4.否则，不是一个有效的名字。

返回 不同 且有效的公司名字的数目。


```
use std::collections::{HashMap, HashSet};

impl Solution {
    pub fn distinct_names(ideas: Vec<String>) -> i64 {
        // 按首字母分组，存储每个首字母对应的后缀集合
        let mut groups: HashMap<char, HashSet<String>> = HashMap::new();
        for idea in ideas {
            let mut chars = idea.chars();
            let first = chars.next().unwrap();
            let suffix = chars.collect::<String>();
            groups.entry(first).or_insert_with(HashSet::new).insert(suffix);
        }

        let letters: Vec<char> = groups.keys().copied().collect();
        let mut ans = 0;

        // 枚举首字母对 (a, b)
        for i in 0..letters.len() {
            for j in i + 1..letters.len() {
                let a = letters[i];
                let b = letters[j];
                let set_a = &groups[&a];
                let set_b = &groups[&b];

                // 计算两个后缀集合的交集大小
                // 交集部分交换后仍会存在于原集合中，不能形成有效名称
                let common = set_a.intersection(set_b).count();

                // 对每对有效组合，可以形成两个方向：
                // a后缀 + b首字母 和 b后缀 + a首字母
                let valid_a = set_a.len() - common;
                let valid_b = set_b.len() - common;
                ans += (valid_a * valid_b) as i64 * 2;
            }
        }

        ans
    }
}
```
