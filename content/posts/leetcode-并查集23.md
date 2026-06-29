---
title: "leetcode-并查集23"
date: 2026-06-25T11:22:22+08:00
tags: ["leetcode", "并查集"]
draft: false
---


## 交换字符串中的元素

给你一个字符串 s，以及该字符串中的一些「索引对」数组 pairs，其中 pairs[i] = [a, b] 表示字符串中的两个索引（编号从 0 开始）。

你可以 任意多次交换 在 pairs 中任意一对索引处的字符。

返回在经过若干次交换后，s 可以变成的按字典序最小的字符串。


```
use std::collections::{HashMap, BinaryHeap};
use std::cmp::Reverse;

impl Solution {
    /// 返回经过任意次交换后字典序最小的字符串。
    /// 将可交换的索引对视为连通关系，每个连通分量内的字符可任意排列，
    /// 按字典序从小到大放置即可。
    pub fn smallest_string_with_swaps(s: String, pairs: Vec<Vec<i32>>) -> String {
        let n = s.len();
        let chars: Vec<char> = s.chars().collect();

        // 1. 并查集构建连通分量
        let mut parent = (0..n).collect::<Vec<_>>();

        /// 查找根节点（路径压缩）
        fn find(parent: &mut Vec<usize>, mut x: usize) -> usize {
            while parent[x] != x {
                parent[x] = parent[parent[x]];
                x = parent[x];
            }
            x
        }

        /// 合并两个集合
        fn union(parent: &mut Vec<usize>, a: usize, b: usize) {
            let ra = find(parent, a);
            let rb = find(parent, b);
            if ra != rb {
                parent[ra] = rb;
            }
        }

        for p in pairs {
            union(&mut parent, p[0] as usize, p[1] as usize);
        }

        // 2. 按连通分量分组收集字符（使用最小堆自动排序）
        let mut groups: HashMap<usize, BinaryHeap<Reverse<char>>> = HashMap::new();
        for (i, &c) in chars.iter().enumerate() {
            let root = find(&mut parent, i);
            groups.entry(root).or_insert_with(BinaryHeap::new).push(Reverse(c));
        }

        // 3. 按原索引顺序输出每个位置的最小字符
        let mut result = String::with_capacity(n);
        for i in 0..n {
            let root = find(&mut parent, i);
            let heap = groups.get_mut(&root).unwrap();
            let Reverse(c) = heap.pop().unwrap();
            result.push(c);
        }

        result
    }
}
```
