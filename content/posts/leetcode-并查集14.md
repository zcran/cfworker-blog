---
title: "leetcode-并查集14"
date: 2026-06-25T11:22:22+08:00
tags: ["leetcode", "并查集"]
draft: false
---


## 相似字符串组

如果交换字符串 X 中的两个不同位置的字母，使得它和字符串 Y 相等，那么称 X 和 Y 两个字符串相似。如果这两个字符串本身是相等的，那它们也是相似的。

例如，"tars" 和 "rats" 是相似的 (交换 0 与 2 的位置)； "rats" 和 "arts" 也是相似的，但是 "star" 不与 "tars"，"rats"，或 "arts" 相似。

总之，它们通过相似性形成了两个关联组：{"tars", "rats", "arts"} 和 {"star"}。注意，"tars" 和 "arts" 是在同一组中，即使它们并不相似。形式上，对每个组而言，要确定一个单词在组中，只需要这个词和该组中至少一个单词相似。

给你一个字符串列表 strs。列表中的每个字符串都是 strs 中其它所有字符串的一个字母异位词。请问 strs 中有多少个相似字符串组？


```
impl Solution {
    pub fn num_similar_groups(strs: Vec<String>) -> i32 {
        let n = strs.len();
        let mut parent = (0..n).collect::<Vec<_>>();
        let mut size = vec![1; n];

        /// 查找根节点，带路径压缩
        fn find(parent: &mut [usize], mut x: usize) -> usize {
            while parent[x] != x {
                parent[x] = parent[parent[x]];
                x = parent[x];
            }
            x
        }

        /// 合并两个集合
        fn union(parent: &mut [usize], size: &mut [usize], x: usize, y: usize) {
            let rx = find(parent, x);
            let ry = find(parent, y);
            if rx == ry {
                return;
            }
            // 按秩合并
            if size[rx] < size[ry] {
                parent[rx] = ry;
                size[ry] += size[rx];
            } else {
                parent[ry] = rx;
                size[rx] += size[ry];
            }
        }

        /// 判断两个字符串是否相似（最多两个位置不同）
        fn is_similar(s1: &str, s2: &str) -> bool {
            let diff = s1
                .bytes()
                .zip(s2.bytes())
                .filter(|(a, b)| a != b)
                .count();
            diff == 0 || diff == 2
        }

        // 遍历所有字符串对，相似则合并
        for i in 0..n {
            for j in i + 1..n {
                if is_similar(&strs[i], &strs[j]) {
                    union(&mut parent, &mut size, i, j);
                }
            }
        }

        // 统计连通分量数量（根节点个数）
        let mut groups = 0;
        for i in 0..n {
            if find(&mut parent, i) == i {
                groups += 1;
            }
        }
        groups
    }
}
```
