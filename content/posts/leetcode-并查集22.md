---
title: "leetcode-并查集22"
date: 2026-06-25T11:22:22+08:00
tags: ["leetcode", "并查集"]
draft: false
---


## 按字典序排列最小的等效字符串

给出长度相同的两个字符串s1 和 s2 ，还有一个字符串 baseStr 。

其中  s1[i] 和 s2[i]  是一组等价字符。

· 举个例子，如果 s1 = "abc" 且 s2 = "cde"，那么就有 'a' == 'c', 'b' == 'd', 'c' == 'e'。

等价字符遵循任何等价关系的一般规则：

 · 自反性 ：'a' == 'a'
 · 对称性 ：'a' == 'b' 则必定有 'b' == 'a'
 · 传递性 ：'a' == 'b' 且 'b' == 'c' 就表明 'a' == 'c'

例如， s1 = "abc" 和 s2 = "cde" 的等价信息和之前的例子一样，那么 baseStr = "eed" , "acd" 或 "aab"，这三个字符串都是等价的，而 "aab" 是 baseStr 的按字典序最小的等价字符串

利用 s1 和 s2 的等价信息，找出并返回 baseStr 的按字典序排列最小的等价字符串。


```
impl Solution {
    /// 返回 baseStr 的按字典序最小的等价字符串。
    /// 利用并查集合并所有等价字符，并确保每个集合的根节点是字典序最小的字符。
    pub fn smallest_equivalent_string(s1: String, s2: String, base_str: String) -> String {
        // 26 个小写字母的并查集
        let mut parent = [0; 26];
        for i in 0..26 {
            parent[i] = i;
        }

        /// 查找根节点（路径压缩）
        fn find(parent: &mut [usize; 26], mut x: usize) -> usize {
            while parent[x] != x {
                parent[x] = parent[parent[x]]; // 隔代压缩
                x = parent[x];
            }
            x
        }

        /// 合并两个集合，总是让字典序较小的作为根
        fn union(parent: &mut [usize; 26], a: usize, b: usize) {
            let ra = find(parent, a);
            let rb = find(parent, b);
            if ra != rb {
                // ra 和 rb 中较小的作为根，保证字典序最小
                if ra < rb {
                    parent[rb] = ra;
                } else {
                    parent[ra] = rb;
                }
            }
        }

        // 1. 合并所有等价字符对
        let bytes1 = s1.as_bytes();
        let bytes2 = s2.as_bytes();
        for i in 0..bytes1.len() {
            let a = (bytes1[i] - b'a') as usize;
            let b = (bytes2[i] - b'a') as usize;
            union(&mut parent, a, b);
        }

        // 2. 将 base_str 的每个字符替换为其集合中的最小字符
        let mut result = String::with_capacity(base_str.len());
        for &c in base_str.as_bytes() {
            let idx = (c - b'a') as usize;
            let root = find(&mut parent, idx);
            result.push((b'a' + root as u8) as char);
        }

        result
    }
}
```
