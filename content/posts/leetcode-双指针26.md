---
title: "leetcode-双指针26"
date: 2026-09-05T09:53:02+08:00
tags: ["leetcode", "双指针"]
draft: false
---


## 分发饼干

假设你是一位很棒的家长，想要给你的孩子们一些小饼干。但是，每个孩子最多只能给一块饼干。

对每个孩子 i，都有一个胃口值 g[i]，这是能让孩子们满足胃口的饼干的最小尺寸；并且每块饼干 j，都有一个尺寸 s[j] 。如果 s[j] >= g[i]，我们可以将这个饼干 j 分配给孩子 i ，这个孩子会得到满足。你的目标是满足尽可能多的孩子，并输出这个最大数值。


```
impl Solution {
    pub fn find_content_children(mut g: Vec<i32>, mut s: Vec<i32>) -> i32 {
        // 排序：胃口和饼干都从小到大
        g.sort_unstable();
        s.sort_unstable();

        let mut child = 0; // 已满足的孩子数量，也是下一个待满足孩子的索引

        // 遍历饼干，尝试满足当前最饥饿的孩子
        for &cookie in &s {
            if child < g.len() && cookie >= g[child] {
                child += 1;
            }
        }

        child as i32
    }
}
```
