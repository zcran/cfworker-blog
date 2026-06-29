---
title: "leetcode-并查集41"
date: 2026-06-25T11:22:23+08:00
tags: ["leetcode", "并查集"]
draft: false
---


## 找出知晓秘密的所有专家


给你一个整数 n ，表示有 n 个专家从 0 到 n - 1 编号。另外给你一个下标从 0 开始的二维整数数组 meetings ，其中 meetings[i] = [xi, yi, timei] 表示专家 xi 和专家 yi 在时间 timei 要开一场会。一个专家可以同时参加 多场会议 。最后，给你一个整数 firstPerson 。

专家 0 有一个 秘密 ，最初，他在时间 0 将这个秘密分享给了专家 firstPerson 。接着，这个秘密会在每次有知晓这个秘密的专家参加会议时进行传播。更正式的表达是，每次会议，如果专家 xi 在时间 timei 时知晓这个秘密，那么他将会与专家 yi 分享这个秘密，反之亦然。

秘密共享是 瞬时发生 的。也就是说，在同一时间，一个专家不光可以接收到秘密，还能在其他会议上与其他专家分享。

在所有会议都结束之后，返回所有知晓这个秘密的专家列表。你可以按 任何顺序 返回答案。

```
use std::collections::{HashMap, VecDeque};
use std::collections::HashSet;
impl Solution {
    pub fn find_all_people(n: i32, mut meetings: Vec<Vec<i32>>, first_person: i32) -> Vec<i32> {
        let n = n as usize;
        let mut knows = vec![false; n];
        knows[0] = true;
        knows[first_person as usize] = true;

        // 按时间排序
        meetings.sort_unstable_by_key(|m| m[2]);

        let mut i = 0;
        let m = meetings.len();

        while i < m {
            let mut j = i;
            // 找到同一时间的所有会议
            while j + 1 < m && meetings[j + 1][2] == meetings[i][2] {
                j += 1;
            }

            // 构建当前时间点的邻接表
            let mut graph: HashMap<i32, Vec<i32>> = HashMap::new();
            let mut participants = Vec::new();

            for k in i..=j {
                let x = meetings[k][0];
                let y = meetings[k][1];
                graph.entry(x).or_insert_with(Vec::new).push(y);
                graph.entry(y).or_insert_with(Vec::new).push(x);
                participants.push(x);
                participants.push(y);
            }

            // BFS：从已知秘密的参与者开始传播
            let mut queue = VecDeque::new();
            let mut visited = HashSet::new();

            for &p in &participants {
                if knows[p as usize] && !visited.contains(&p) {
                    visited.insert(p);
                    queue.push_back(p);
                }
            }

            while let Some(u) = queue.pop_front() {
                if let Some(neighbors) = graph.get(&u) {
                    for &v in neighbors {
                        if !visited.contains(&v) {
                            visited.insert(v);
                            knows[v as usize] = true;
                            queue.push_back(v);
                        }
                    }
                }
            }

            i = j + 1;
        }

        // 收集结果
        (0..n).filter(|&i| knows[i]).map(|i| i as i32).collect()
    }
}
```
