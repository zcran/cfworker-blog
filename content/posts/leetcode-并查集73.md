---
title: "leetcode-并查集73"
date: 2026-06-25T11:22:24+08:00
tags: ["leetcode", "并查集"]
draft: false
---


## 婴儿名字

每年，政府都会公布一万个最常见的婴儿名字和它们出现的频率，也就是同名婴儿的数量。有些名字有多种拼法，例如，John 和 Jon 本质上是相同的名字，但被当成了两个名字公布出来。给定两个列表，一个是名字及对应的频率，另一个是本质相同的名字对。设计一个算法打印出每个真实名字的实际频率。注意，如果 John 和 Jon 是相同的，并且 Jon 和 Johnny 相同，则 John 与 Johnny 也相同，即它们有传递和对称性。

在结果列表中，选择 字典序最小 的名字作为真实名字。


```
use std::collections::HashMap;

impl Solution {
    /// 计算真实名字频率（并查集 + 字典序最小根）
    pub fn truly_most_popular(names: Vec<String>, synonyms: Vec<String>) -> Vec<String> {
        // 1. 解析名字频率
        let mut freq: HashMap<String, i32> = HashMap::new();
        for name in names {
            let (n, f) = Self::parse_name_freq(&name);
            freq.insert(n, f);
        }

        // 2. 并查集父节点映射
        let mut parent: HashMap<String, String> = HashMap::new();

        // 带路径压缩的查找（递归版，避免可变借用冲突）
        fn find(parent: &mut HashMap<String, String>, x: String) -> String {
            if let Some(p) = parent.get(&x) {
                if p != &x {
                    let root = find(parent, p.clone());
                    parent.insert(x.clone(), root.clone());
                    return root;
                }
            }
            x
        }

        fn union(parent: &mut HashMap<String, String>, freq: &mut HashMap<String, i32>, a: String, b: String) {
            let ra = find(parent, a);
            let rb = find(parent, b);
            if ra == rb {
                return;
            }

            // 字典序较小的作为根
            let (root, child) = if ra < rb { (ra, rb) } else { (rb, ra) };
            parent.insert(child.clone(), root.clone());

            // 合并频率
            let sum = freq.get(&root).unwrap_or(&0) + freq.get(&child).unwrap_or(&0);
            freq.insert(root, sum);
            freq.remove(&child);
        }

        // 3. 处理同义词对
        for s in synonyms {
            let (a, b) = Self::parse_synonym(&s);
            union(&mut parent, &mut freq, a, b);
        }

        // 4. 生成结果
        let mut res = Vec::with_capacity(freq.len());
        for (name, &count) in &freq {
            res.push(format!("{}({})", name, count));
        }
        res
    }

    /// 解析 "Name(freq)" 格式，返回 (名字, 频率)
    fn parse_name_freq(s: &str) -> (String, i32) {
        let idx = s.find('(').unwrap();
        let name = &s[..idx];
        let freq = s[idx + 1..s.len() - 1].parse().unwrap();
        (name.to_string(), freq)
    }

    /// 解析同义词对 "(a,b)" 格式，返回 (a, b)
    fn parse_synonym(s: &str) -> (String, String) {
        let s = &s[1..s.len() - 1];
        let mid = s.find(',').unwrap();
        (s[..mid].to_string(), s[mid + 1..].to_string())
    }
}
```
