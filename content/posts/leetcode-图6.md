---
title: "leetcode-图6"
date: 2026-07-27T10:01:35+08:00
tags: ["leetcode", "图"]
draft: false
---


## 找到小镇的法官

小镇里有 n 个人，按从 1 到 n 的顺序编号。传言称，这些人中有一个暗地里是小镇法官。

如果小镇法官真的存在，那么：

1. 小镇法官不会信任任何人。
2. 每个人（除了小镇法官）都信任这位小镇法官。
3. 只有一个人同时满足属性 1 和属性 2 。

给你一个数组 trust ，其中 trust[i] = [ai, bi] 表示编号为 ai 的人信任编号为 bi 的人。

如果小镇法官存在并且可以确定他的身份，请返回该法官的编号；否则，返回 -1 。


```
impl Solution {
    pub fn find_judge(n: i32, trust: Vec<Vec<i32>>) -> i32 {
        // 每个人的入度（被信任次数）和出度（信任他人次数）
        let mut in_degree = vec![0; n as usize + 1];
        let mut out_degree = vec![0; n as usize + 1];

        for t in trust {
            let (a, b) = (t[0] as usize, t[1] as usize);
            out_degree[a] += 1;
            in_degree[b] += 1;
        }

        // 法官满足：入度 = n-1（所有人都信任他），出度 = 0（不信任任何人）
        for i in 1..=n as usize {
            if in_degree[i] == n - 1 && out_degree[i] == 0 {
                return i as i32;
            }
        }

        -1
    }
}
```
