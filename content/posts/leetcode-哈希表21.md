---
title: "leetcode-哈希表21"
date: 2026-10-09T20:07:56+08:00
tags: ["leetcode", "哈希表"]
draft: false
---


## 翻转卡片游戏

在桌子上有 n 张卡片，每张卡片的正面和背面都写着一个正数（正面与背面上的数有可能不一样）。

我们可以先翻转任意张卡片，然后选择其中一张卡片。

如果选中的那张卡片背面的数字 x 与任意一张卡片的正面的数字都不同，那么这个数字是我们想要的数字。

哪个数是这些想要的数字中最小的数（找到这些数中的最小值）呢？如果没有一个数字符合要求的，输出 0 。

其中, fronts[i] 和 backs[i] 分别代表第 i 张卡片的正面和背面的数字。

如果我们通过翻转卡片来交换正面与背面上的数，那么当初在正面的数就变成背面的数，背面的数就变成正面的数。


```
use std::collections::HashSet;

impl Solution {
    pub fn flipgame(fronts: Vec<i32>, backs: Vec<i32>) -> i32 {
        // 若某张卡片正反面相同，该数字无法被翻到背面（另一面仍是它），
        // 因此只要它出现在任意卡片正面，就不能作为答案，先记录这些"坏"数字。
        let bad: HashSet<i32> = fronts
            .iter()
            .zip(&backs)
            .filter(|&(&f, &b)| f == b)
            .map(|(&f, _)| f)
            .collect();

        // 对每张卡片，正反面中所有不在 bad 里的数字都是候选，取最小值。
        fronts
            .iter()
            .chain(&backs)
            .filter(|&&v| !bad.contains(&v))
            .min()
            .copied()
            .unwrap_or(0)
    }
}
```
