---
title: "leetcode-动态规划182"
date: 2026-10-05T20:40:06+08:00
tags: ["leetcode", "动态规划"]
draft: false
---


## 秋叶收藏集

小扣出去秋游，途中收集了一些红叶和黄叶，他利用这些叶子初步整理了一份秋叶收藏集 leaves， 字符串 leaves 仅包含小写字符 r 和 y， 其中字符 r 表示一片红叶，字符 y 表示一片黄叶。 出于美观整齐的考虑，小扣想要将收藏集中树叶的排列调整成「红、黄、红」三部分。每部分树叶数量可以不相等，但均需大于等于 1。每次调整操作，小扣可以将一片红叶替换成黄叶或者将一片黄叶替换成红叶。请问小扣最少需要多少次调整操作才能将秋叶收藏集调整完毕。


```
impl Solution {
    pub fn minimum_operations(leaves: String) -> i32 {
        let s = leaves.as_bytes();
        let n = s.len();

        // g[i] = 前缀 [0..=i] 的"红黄平衡"累加值：
        //   初始 g[0] = (s[0]=='y') ? 1 : -1
        //   每步 g += 2*isYellow - 1
        // gmin 为前缀中 g 的最小值，ans 记录 gmin - g 的最小值，
        // 最终答案为 ans + (g + n) / 2。
        let mut g = if s[0] == b'y' { 1 } else { -1 };
        let mut gmin = g;
        let mut ans = i32::MAX;

        for i in 1..n {
            let is_yellow = if s[i] == b'y' { 1 } else { 0 };
            g += 2 * is_yellow - 1;
            if i != n - 1 {
                ans = ans.min(gmin - g);
            }
            gmin = gmin.min(g);
        }

        ans + (g + n as i32) / 2
    }
}
```
