---
title: "leetcode-广度优先搜索7"
date: 2026-09-13T09:32:06+08:00
tags: ["leetcode", "广度优先搜索"]
draft: false
---


## 最小基因变化

基因序列可以表示为一条由 8 个字符组成的字符串，其中每个字符都是 'A'、'C'、'G' 和 'T' 之一。

假设我们需要调查从基因序列 start 变为 end 所发生的基因变化。一次基因变化就意味着这个基因序列中的一个字符发生了变化。

例如，"AACCGGTT" --> "AACCGGTA" 就是一次基因变化。

另有一个基因库 bank 记录了所有有效的基因变化，只有基因库中的基因才是有效的基因序列。（变化后的基因必须位于基因库 bank 中）

给你两个基因序列 start 和 end ，以及一个基因库 bank ，请你找出并返回能够使 start 变化为 end 所需的最少变化次数。如果无法完成此基因变化，返回 -1 。

注意：起始基因序列 start 默认是有效的，但是它并不一定会出现在基因库中。


```
use std::collections::{HashSet, VecDeque};

impl Solution {
    pub fn min_mutation(start: String, end: String, bank: Vec<String>) -> i32 {
        // 终点必须在基因库中，否则无法到达
        let bank: HashSet<String> = bank.into_iter().collect();
        if !bank.contains(&end) {
            return -1;
        }
        // 起点等于终点，无需变化
        if start == end {
            return 0;
        }

        // 预生成基因库中每个序列的“通配符”模式，用于快速定位邻居
        // 例如 "AACCGGTT" -> "*ACCGGTT", "A*CCGGTT", ...
        let mut patterns: HashSet<String> = HashSet::with_capacity(bank.len() * 8);
        for gene in &bank {
            let mut bytes = gene.clone().into_bytes();
            for i in 0..bytes.len() {
                let old = bytes[i];
                bytes[i] = b'*';
                patterns.insert(unsafe { String::from_utf8_unchecked(bytes.clone()) });
                bytes[i] = old;
            }
        }

        // BFS：从起点出发，每次变化一个字符
        let mut visited: HashSet<String> = HashSet::new();
        let mut queue: VecDeque<(String, i32)> = VecDeque::new();
        queue.push_back((start.clone(), 0));
        visited.insert(start);

        while let Some((gene, step)) = queue.pop_front() {
            let mut bytes = gene.clone().into_bytes();

            for i in 0..bytes.len() {
                let old = bytes[i];

                for &c in b"ACGT" {
                    if c == old {
                        continue;
                    }
                    bytes[i] = c;
                    let next = unsafe { String::from_utf8_unchecked(bytes.clone()) };

                    // 只考虑基因库中且未访问过的序列
                    if bank.contains(&next) && visited.insert(next.clone()) {
                        if next == end {
                            return step + 1;
                        }
                        queue.push_back((next, step + 1));
                    }
                }

                bytes[i] = old;
            }
        }

        -1
    }
}
```
