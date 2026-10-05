---
title: "leetcode-贪心67"
date: 2026-10-03T12:55:38+08:00
tags: ["leetcode", "贪心"]
draft: false
---


## 需要教语言的最少人数

在一个由 m 个用户组成的社交网络里，我们获取到一些用户之间的好友关系。两个用户之间可以相互沟通的条件是他们都掌握同一门语言。

给你一个整数 n ，数组 languages 和数组 friendships ，它们的含义如下：

· 总共有 n 种语言，编号从 1 到 n 。
· languages[i] 是第 i 位用户掌握的语言集合。
· friendships[i] = [ui, vi] 表示 ui 和 vi 为好友关系。

你可以选择 一门 语言并教会一些用户，使得所有好友之间都可以相互沟通。请返回你 最少 需要教会多少名用户。

请注意，好友关系没有传递性，也就是说如果 x 和 y 是好友，且 y 和 z 是好友， x 和 z 不一定是好友。


```
use std::collections::{HashMap, HashSet};

impl Solution {
    pub fn minimum_teachings(n: i32, languages: Vec<Vec<i32>>, friendships: Vec<Vec<i32>>) -> i32 {
        // 找出所有「无法沟通」的 friendships，收集涉及的用户（去重）
        let mut need_teach: HashSet<usize> = HashSet::new();
        for f in &friendships {
            let (u, v) = (f[0] as usize - 1, f[1] as usize - 1);
            // 两人没有共同语言则至少一人需要被教
            if languages[u].iter().all(|l| !languages[v].contains(l)) {
                need_teach.insert(u);
                need_teach.insert(v);
            }
        }

        // 没有需要处理的好友对，直接返回 0
        if need_teach.is_empty() {
            return 0;
        }

        // 选一门语言，使它在这些用户中掌握的人数最多，其余人各教一次
        let mut lang_count: HashMap<i32, i32> = HashMap::new();
        let mut max_count = 0;
        for &person in &need_teach {
            for &lang in &languages[person] {
                let c = lang_count.entry(lang).or_insert(0);
                *c += 1;
                max_count = max_count.max(*c);
            }
        }

        need_teach.len() as i32 - max_count
    }
}
```
